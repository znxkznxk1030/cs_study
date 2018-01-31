# DI(Dependency Injection) 이란?



DI는 객체간 느슨한 결합을 만들기 위해 고안된 디자인 패턴중 하나이다.



##  강한 결합(Tight Coupling) vs 느슨한 결합(Loose Coupling) 

1. 강한 결합

   강하게 결합된 두 객체는 서로 많은 정보를 필요로 하고, 인터페이스 간에 높은 의존성을 가진다.

   이는 하나의 객체에서 변경이 될을 시 이에 의존성을 가진 많은 객체들에게도 변경을 요구하게 된다.

## 예시

```typescript
class Remote() {
  constructor() {
    this.homeApplicaion = new Television();
  }
  run() {
    this.homeApplication.run();
  }
}
  
class Television() {
  constructor() {}
  
  run() {
    console.log('TV is stared');
  }
}

class Aircondition(){
  constructor() {}
  
  start() {
    console.log('A air-condition is started');
  }
}

var remoteController = new Remote();
Remote.homeApplication.run();
```

2. 느슨한 결합

   느슨한 결합은 앞서 말한 강한 결합의 의존성 문제를 해결하는 것이 목적이다. 느슨한 결합으로 결합된 객체(또는 컴포넌트)들은 서로간 변경사항이 있더라도 다른 컴포넌트의 변경을 방지 할 수 있게되어, 전체 프레임워크의 안정성을 증가 시킨다.

## 예시

```typescript
interface HomeApplication() {
  run?: any;
}
  
class Remote(option) {
  homeApplication: HomeApplicaion;
  
  constructor(){
    switch(option.kind){
      case 'television' : this.homeApplication.push(new Television());
        break;
      case 'aircondition' : this.homeApplication.push(new Aircondition());
        break;
      
    }
  }
  
  run(){
  	this.homeApplication.forEach(() => this.run() );
	}
}

class Television implements HomeApplication {
  constructor() {}
  run: function () {
    console.log('A TV is started!');
  }
}

class Aircondition implements HomeApplication {
  constructor() {}
  
  run: function () {
    console.log('A air-condition is started!');
  }
}
  

var remoteController = new Remote({kind: 'television'});
Remote.homeApplication.run();

```



## DI의 장점

1. **Unit Testing.**

   ```Javascript
   describe('CardService', () => {
     beforeEach(() => {
       TestBed.configureTestingModule({
         providers: [CardService]
       });
     });

     it('should be created', inject([CardService], (service: CardService) => {
       expect(service).toBeTruthy();
     }));
   });
   ```

2. **Validation/Exception Management.**