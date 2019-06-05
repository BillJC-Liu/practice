# 冒泡排序如何实现，时间复杂度是多少，还能如何改进？

```javascript
  function maopao(arr){
    const array = [...arr]
    for(let i = 0, len = array.length; i < len - 1; i++){
      for(let j = i + 1; j < len; j++) {
        if (array[i] > array[j]) {
          let temp = array[i]
          array[i] = array[j]
          array[j] = temp
        }
      }
    }
    return array
  }

  // 优化版
   function maopao(arr){
    const array = [...arr]
    let isOk = true
    for(let i = 0, len = array.length; i < len - 1; i++){
      for(let j = i + 1; j < len; j++) {
        if (array[i] > array[j]) {
          let temp = array[i];
          isOk = false;
          array[i] = array[j];
          array[j] = temp;
        }
      }
      if(isOk){
        break;
      }
    }
    return array
  }
  

```