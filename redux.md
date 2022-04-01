# redux

## useEffect

> useEffect는 React Hook의 한 종류이다.
> useEffect => 컴포넌트가 나타날때, 컴포넌트가 사라질때 작업처리 가능

'''
useEffect(() => {
    console.log("렌더링 시 한번만 실행");
}, []);
'''
> 배열인 []만을 넣어주면 useEffect 안의 코드는 컴포넌트가 화면에 나올때 단 한번만 실행이 되죠.

'''
const [value, setValue] = useState('안녕');

useEffect(() => {
  console.log("해당 props가 변경될때 마다 실행");
}, [value]);
'''

> 다음은 특정 props가 변경 될때마다 실행되는 코드입니다. 
> 이 때 주의해야할 점은 특정 props가 변경 될때도 실행이 되고 위에서 언급했던 것 처럼 마운트 될때 최초로 실행도 한번 됩니다. 
> 두번째 파라매터인 배열에 특정 props를 넣어주면 됩니다.

