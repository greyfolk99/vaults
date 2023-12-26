

JavaScript 파일명 포맷 방식

여러 args로 가독성 좋게 fomatting 하기 좋은 방식
```javascript
const format = "{yyyy}_{mm}"  
const kwargs = { "yyyy" : "2022", "mm" : "08" } 
  
const parsed = format.replace(/{(.*?)}/g, (match, key) => (kwargs[key] || match));
if (parsed.includes("{")) throw new Error("Invalid format");  

console.log(parsed);
```

TypeScript를 사용하면 키 오류는 잡아낼 수 있음
```typescript
type Kwargs  
	= { "yyyy" : string , "mm" : string }  

const format  
	: `{${keyof Kwargs}}_{${keyof Kwargs}}` // String Literal Type  
	= "{yyyy}_{mm}" 122 
const kwargs  
	: Kwargs  
	= { "yyyy" : "2022" , "mm" : "08" }  

const parsed = format.replace(/{(.*?)}/g, (match, key) => (kwargs as any)[key]) 

console.log(parsed)
```