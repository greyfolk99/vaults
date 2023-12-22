


scratch 1:
여러 args로 가독성 좋게 fomatting 하기 좋은 방식
```javascript

const key1 = "yyyy"
const post1
const key2 = "mm"  

const value1 = "2022"  
const value2 = "08"  

const format = "{yyyy}_{mm}"

const parsed = format    
	.replace(`{${key1}}`, value1)
    .replace(`{${key2}}`, value2)
console.log(format);


```