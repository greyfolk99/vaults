> 낙관적 동시성 제어

낙관적 동시성 제어는 사용자들이 동시에 데이터를 수정하지 않을 것이라고 가정한다. 따라서 데이터를 읽을때는 Lock을 설정하지 않는다.  
그러므로 데이터를 **수정하고자 하는 시점에 앞서 반드시 읽은데이터가 다른 사용자에 의해 변경 되었는지를 검사**해야 한다.k