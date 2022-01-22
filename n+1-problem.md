# N+1 문제

연관관계를 가지는 데이터를 조회할 때 해당 데이터를 조회하는 쿼리가 1회, 그리고 해당 데이터와 연관된 데이터를 조회하는 쿼리가 로우 당 1회로 총 N회 발생하는 문제이다. 

일반적으로 발생하는 ORM의 lazy loading 으로 인한 N+1 문제를 살펴보자.
> 사실 이 외의 케이스는 아직 잘 모른다. N+1 문제를 발생시키는 유인이 ORM의 lazy loading 뿐인지 확신이 없다.


### N+1,lazy loading, eager loaing

Company : Employee = 1:N 관계의 데이터를 생각해보자.

```ts
@Entity('COMPANY')
export class Company extends TimeStamped {
	@PrimaryGeneratedColumn('increment')
	companyId: number;

	@Column({ type: 'varchar' })
	companyName: string;

	@OneToMany(() => Employee, (employee) => employee.company, {
		onDelete: 'CASCADE'
	})
	employee: Employee[];
}

@Entity('EMPLOYEE')
export class Employee extends TimeStamped {
	@PrimaryGeneratedColumn('increment')
	employeeId: number;

	@Column({ type: 'varchar' })
	employeeName: string;

	@ManyToOne(() => Company, (company) => company.employee)
	@JoinColumn([{ name: 'companyId', referencedColumnName: 'companyId' }])
	company: Company;
}
```

일반적으로 ORM은 최대한 쿼리를 미뤘다가 꼭 필요한 시점에 실제로 쿼리를 날리는데 이를 lazy loading 이라고 한다. 

```ts
async findAll(id:number) {
    // typeorm 에서는 lazy loading을 위해 promise를 사용함
    const companies = await this.companyRepository.findAll(id); // 여기서 lazy loading은 employee는 신경쓰지 않고 company만 쿼리한다.
    return companies;
}
```

만약 lazy loading 으로 가져온 데이터와 연관관계에 있는 데이터를 가져오기 위해서는 다시 쿼리를 날리게 된다.

```ts
async findAllWithEmployee(id:number) {
    const companies = await this.companyRepository.findAll(id); 
    let companiesWithEmployee;

    for (const company of await companies) {
        const hasEmployee = (await company.employee).length > 0 // 여기서 employee 를 찾기위해 추가적으로 쿼리한다. 즉 쿼리 된 companies의 수 N 만큼의 쿼리가 또 발생하는 것. 
        companiesWithEmployee.push(company)
    }

    return companiesWithEmployee
}
```

이게 바로 N+1 문제이다. ( 1+N이 더 직관적이지 않나? 열받는다. )

이를 해결하기 위해서는 결국 기본적으로 소극적으로 쿼리하게 되어있는 ORM을 조작해 필요한 데이터를 한번에 가져와야한다.

- entity를 정의할때 eager 옵션을 주거나,
    - 제약조건이 있음
- 쿼리빌더를 쓰거나
    - 직접 조인을 걸어주면 됨



### ref
- https://incheol-jung.gitbook.io/docs/q-and-a/spring/n+1
- https://jojoldu.tistory.com/165
- https://tristy.tistory.com/m/36