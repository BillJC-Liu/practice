# TS学习
## 高级类型
### 交叉类型

概念：将多个类型合并成为一个新的类型，新类型具有所有类型的特性。是多个类型的**并集**不是**交集**

```javascript
  interface IInterfaceA { methodA:()=> void  }
  interface IInterfaceB { methodB:()=> void  }
  
  let obj : IInterfaceA & IInterfaceB =  {
    methodA:()=> console.log(1);
    methodB:()=> console.log(2);
  }
```

###联合类型

概念：多个类型中满足其一

```javascript 
  let a : string | object = 1

  interface Bird {
    fly();
    layEggs();
  }

  interface Fish {
    swim();
    layEggs();
  }

  function getSmallPet(): Fish | Bird {
    return
  }

  let pet = getSmallPet();
  // okey
  pet.layEggs(); 
  // 需要类型断言 否则提示错误
  (<Fish>pet).swim();   
```

### 
