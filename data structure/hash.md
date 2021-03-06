# hash table
최근 문제해결능력을 향상시키기 위해(사실은 취직이 목적이지만 ㅎ)
코팅테스트 문제를 풀고있다. 오늘은 프&#45;그래&#45;머스라는 곳에서 "완주하지 못한 선수"라는 제목부터 매우 흥미로운 문제를 풀어보았다. 쉬운 수준임에도 제대로 풀지못하는 나를 보며 이대로는 안되겠다 싶어 이제는 알고리즘과 자료구조를 공부하려고한다. 단순히 공부하고 지나치는게 아니라 코드를 구현해보고 원리를 파악해서 실무에서도 사용할 수 있는 능력을 갖추는 것이 최종 목표다.


## why is hash table fast?
```
const arr1 = ['kim','lee','park'];
const arr2 = ['lee','park'];
for(personOfarr1 of arr1){
  for(personOfarr2 of arr2){
    if(personOfarr1 === personOfarr2)
      console.log(personOfarr1)
  }
}
```
arr2내의 데이터들이 arr1내에 존재하는지 확인하려면 arr2의 각 요소가 arr1에 있는지 순회하는 시간(O(n) * arr2의 수만큼 걸리게 된다. 최대 3&#42;2의 시간이 걸린다. 즉 O(n^2)의 시간복잡도를 가지기 때문에 데이터가 많아질 수록 탐색 시간이 더 많이 걸릴 가능성이 크다.

하지만 아래와 같은 경우는 어떨까?
key값을 마치 주소처럼 사용해서 key에 해당하는 value를 찾을 수 있다면 오직 이 탐색건만 치면 O(1)의 시간이 걸린다.
즉 arr2를 순회하는 O(n)의 시간복잡도가 걸리기 때문에 보다 더 빠르게 찾을 수 있게 된다.
```
participants = ['kim','lee','park'], completions = ['lee','park'];

let nonCompletion = ''
function hashing(key){  // 해시함수를 통해 key를 받아 해시값을 리턴한다. 실제로는 암호화 알고리즘이 사용됨.
  if(key === 'kim'){
    return 123;
  }else if(key === 'lee'){
    return 456;
  }else if(key ==='park'){
    return 789;
  }
}

let hashTable = new Map([ // 해시테이블은 해시값을 색인(index)으로하여 key에 해당하는 value를 저장하는 자료구조이다.
  [456, 'lee'],
  [789, 'park']
])

for(participant of participants){
  const key = hashing(participant);
  const value = hashTable.get(key);
  if(!value){
    nonCompletion = value;
    break;
  }
}
```
## collesion
이와 같이 hash table은 key값을 hashing하여 얻은 hash 값을 index로 사용하여 데이터를 검색하는 구조를 가지고 있다.
즉 hashing 과정에서 사용되는 알고리즘에 따라 index 값이 정해지기에 입력값의 범위는 무한하지만 출력값은 결과적으로 유한한 범위를 가지게 된다. 예를 들어 1234567 과 2345987 두 개의 key가 있다. 이를 10으로 나누는 알고리즘을 사용하여 hashing하면 두 데이터의 index는 모두 7이 되고 이미 테이블의 7번 방에 두 개의 데이터가 들어가야 한다. 하지만 7로 끝나는 수가 얼마나 많겠는가? 셀 수 없을 정도로
많다. 이렇게 한 방에 두 개 이상의 데이터가 들어가게 되면 어떻게 될까? 이를 collision 충돌이 발생했다고 하는데, key 1234567에 해당하는 index 7과 2345987에 해당하는 index 7 모두 해시테이블의 7번 방에서 데이터를 불러와야 하는 문제점이 생긴다.
이런 상황을 해결하는 몇 가지 방법이 있다.

### 1. Chaning
Buckets(데이터가 저장되는 공간) 한 index에 해당하는 Bucket을 연결리스트로 구성하여 충돌이 발생하면 리스트의 뒤쪽에 하나씩 추가하는 방식.
### 2. Open Addressing
충돌이 일어나면 다른 빈 버킷에 데이터를 저장한다.

그래서 충돌해결 방법에 앞서 최대한 충돌이 발생하지 않고 데이터들이 한쪽에 치우치지 않는 견고한 hashing 알고리즘을 사용하는 것이 우선적으로 매우 중요하다. 또한 어떤 알고리즘을 쓰더라도 테이블의 크기가 작다면 그 만큼 충돌 확률이 높아지고 크다면 메모리 공간을 끝도없이 차지할 것이기에 테이블 크기를 적절하게 설정하는 것도 고려해야 한다.
충돌에 대한 더 깊은 이해를 위해 적재율이라는 개념을 살펴보자. 적재율이란 해시 테이블의 크기 대비, 키의 개수를 말한다.
즉, 키의 개수를 `K`, 해시 테이블의 크기를 `T` 이라고 했을 때 적재율은 K/T 이다. 따라서 적재율이 1을 초과한 해시 테이블의 경우는 반드시 충돌이 발생한다고 볼 수 있다.
충돌이 발생하지 않는 이상적인 해시 테이블의 경우 삽입, 삭제, 탐색 연산은 모두 O(1)로 수행되지만 충돌이 발생할 경우 그 버킷 안에서 다시 한번 탐색해야하기 때문에 최악의 경우 **O(K)** 만큼 걸리게 된다. 따라서 해시 테이블의 중점사항은 앞서 말한 것처럼 index를 고르게 분배해내는 알고리즘을 선택하는 것이라 볼 수 있다.

간단하게 chaining을 통해 해시 테이블을 구현해 보았다.
## let's make a hash table.
1. 기본적으로 데이터를 담을 공간인 (this.data)buckets과 bucket의 크기를 지정할 size가 필요하다.

```
constructor(size){
  this.data = []; // buckets
  if (size) { // 해시 테이블의 크기
    this.size = size;
  } else {
    this.size = 100;
  }
  
  
```
간단한 알고리즘 구현, 인덱스 생성, 데이터 저장, 삭제, 검색 메소드를 구현해보자.
```
hashing(key) { // 아스키코드 값을 모두 더해 해시값을 만들어 냈다.
  let hash = 0;
  for(let char of key){
    hash += char.charCodeAt();
  }
  return hash
}
convertToIndex(hash){ // hash 값을 size로 나눈 나머지를 index로 반환한다.
  return hash % this.size
}
```
입력은 3가지 경우로 생각해 볼 수 있다.
1. bucket이 null인 경우 => 새로운 LinkedList 생성 및 저장
2. bucket에 LinkedList가 이미 존재하고 그 안에 key값에 해당하는 자료가 없는 경우. => LinkedList의 append 메소드로 데이터 연결
3. bucket에 LinkedList가 이미 존재하고 그 안에 key값에 해당하는 자료가 이미 있는 경우 => append 메서드로 데이터 수정
```
put(key, value){
  const hash = this.hashing(key);
  const index = this.converToIndex(hash);
  const list = this.data[index];
  if(list === undifined){ // 1
    this.data[index] = new LinkedList(key, value);
  } else { //2,3
    list.append(key,value)
  }
}
```
get 메소드를 구현할 때 생각해 봐야할 점은 put과 비슷하다. LinkedList 내의 search 메소드로 노드를 검색해서 반환받은 node의 value를 구해야하기 때문에 search 메소드를 사용하기 위해 LindedList가 우선 있는지 확인할 필요가 있다.
```
get(key){
  const hash = this.hashing(key);
  const index = this.convertToIndex(hash):
  const list = this.data[index];
  if (list === undifined) {
    return false
  } else if(!list.search(key)){
    return false
  } else { 
    return list.search(key).value
  }
}
```
삭제 메소드
```
delete(key){
  const hash = this.hashing(key);
  const index = this.convertToIndex(hash);
  const list = this.data[index];
  if (list === undifined) {
    return false;
  } else {
    list.delete(key);
  }
  if (list && !list.head) {
    delete this.data[index];
  }
}
```
최종 HashTable 클래스 코드
```
class HashTable{
  constructor(size){
    this.data = [];
    if (size) {
      this.size = size;;
    } else {
      this.size = 100;
    }
  }

  hashing(key){
    let hash = 0;
    for(let char of key){
      hash += char.charCodeAt();
    }
    return hash
  }

  convertToIndex(hash){
    return hash % this.size
  }

  get(key){
    const hash = this.hashing(key);
    const index = this.convertToIndex(hash);
    const list = this.data[index];
    if (list === undefined) {
      // if that particular table index is undefined, linked-list is not exist,
      // therefore there is no key assgined.
      return false;
    } else if (list.search(key) === false) {
      // the value doesn't exist in the linked-list
      return false;
    } else {
      return list.search(key).value;
    }
  }
  put(key, value){
    const hash = this.hashing(key);
    const index = this.convertToIndex(hash);
    const list = this.data[index];
    if (list === undefined) {
      this.data[index] = new LinkedList(key, value);
    } else {
      this.data[index].append(key, value);
    }
  }
  delete(key){
    const hash = this.hashing(key);
    const index = this.convertToIndex(hash);
    const list = this.data[index];
    if (list) {
      list.delete(key);
    }
    if (list && !list.head) {
      delete this.data[index];
    }
  }
}
```

### 느낀점
지금까지 자료구조에 대한 중요성을 인지하지 못했다. 나도 모르게 내 마음속에서 컴퓨터 하드웨어의 발전으로 자료구조는 사실상 크게 중요하지 않은 이론상의 전유물이 되었다고 생각해온 것 같다. 해시테이블을 공부하면서 적절한 자료구조를 때에 맞게 사용하면 성능면에서 완전히 다른 결과물이 된다는 것을 보고 그 생각이 완전히 뒤바꼈다. 지금이라도 자료구조의 중요성을 알고 활용할수 있게 생각을 달리하는 자세를 깨우쳐서 다행이라고 생각한다. + 어디에 쓰일지 생각해보면 DNS와 같은 Dotain to IP 1:1 매칭 구조에서 뿐만아니라 이 자료구조의 특성인 중복데이터를 확인하기 쉽다는 점을 활용할수 있는 곳이면 매우 요긴하게 쓰일 듯 하다.
