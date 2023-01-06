### useEffect의 용도
예를 들어, const data = React.useState(); 
          console.log(data)를 하게 되면
          > (2) [undefined, f] 
이런 식으로 출력하게 되는데, 이 useEffect의 역할은 배열 안에서 왼쪽에는 data를 주고, 그 data 값을 바꿀 수 있는 함수를 오른쪽에서 줄 수 있다. 
그래서 기존에 JSX를 쓸 때는, 
let counter = 0;
function countUp(){

} 이런 식으로 함수와 변수를 만들고 지정한 뒤, 값을 바뀌게 만드는 함수에 리렌더링 코드를 작성해서 넣어줘야 했다. 하지만 useEffect 사용하면 곧 바로 지정할 데이터와 해당 데이터를 변경할 함수를 지정하고 이 문제를 해결할 수 있다.
-> useEffect를 통해 출력되는 배열의 첫번째 값은 초기값이고, 두번째 요소는 그 값을 바꾸는 함수이다. 

그러나, 우리가 이 useEffect를 통해 데이터를 활용할 때, 일일이 배열의 요소를 인덱스로 전달해주는 것은 불편할 수 있다. 어떻게 하면 배열에서 요소들을 꺼내서 이름을 부여할 수 있을까? 

const food = ["tomato", "potato"] 이렇게 만들었다고 가정하면, 내가 food 외부에서 tomato라는 값을 받으려면 food[0]를 사용하면 된다.  근데 만약 이걸 사용하고 싶으면 const tomato = food[0] 이런식으로 담아야 한다.
그리고 값을 바꿀 때는 const modifier = food[1] 이렇게 지정해서 바꿀 수 있지만, 귀찮고 번거롭다. 그래서 이 방식 대신 우리기 쉽게 할 수 있는 방법을 살펴보자.

const [myFavFood, mySecondFavFood] = food;
이런 식으로 작성하면, 이제 myFavFood는 tomato가 될 것이고, mySecondFavFood는 potato가 될 것이다. 
따라서 이전처럼 하나하나 지정하는 것이 아닌,
let [counter, modifier] = React.useState() 이런 식으로 작성하면 된다.

이번에는 왜 modifier가 필요하고 어떻게 활용할 수 있을지 알아보자. 
let [counter, modifier] = React.useState()
여기서
const onClick = () =>{
    modifier(12354263)
} 함수를 만들면, 이 onClick은 우리가 전에 했던 거와 똑같은 역할을 할 것이다. (클릭하면, counter+1을 할당해주는..) 그런데 원래는 onClick 이벤트를 일으키고 나서 변경된 값을 보여주기 위해서는 리렌더링 과정을 reactDOM을 통해 해줬어야 했는데, 이제는 그럴 필요가 없어졌다. modifier 함수가 존재하는 이유가 바로 이것이다. modifier 함수는 값을 받아서 어떤 값을 부여하던 그 값으로 업데이트하고 리렌더링을 일으킬 것이다. 

정리하면, ReactuseState 함수는 counter 같은 데이터를 숫자형 데이터로 건네줄 것이고, 그 데이터 값을 바꿀 함수도 함께 주어서 이 함수를 이용해 데이터를 바꿔 컴포넌트를 동시에 리렌더링 할 수 있는 것이다!

그래서 기존에 만들었던 코드를 다음과 같이 수정할 수 있다.
const root = document.getElementById('root');
function App(){
    const [counter, setCounter] = React.useState(0)
    const onClick = () = >{
        setCounter(counter + 1);
    };
    return (
        <div>
            <h3>Total clicks: {counter}</h3>
            <button onCLick={onClick}>Click me</button>
        </div>
    );
}
ReactDOM.render(<App />, root);

우리는 counter 라는 데이터를 받아서 return()에 그 데이터를 담고 있다. 그말인 즉, 이게 사용자가 보게 될 컴포넌트인 것이고, 버튼이 클릭되면 counter 값을 바꿔줄 함수를 호출할 것이다. setCounter가 counter의 값을 새롭게 바꿔줄 것이므로 결국에는 같은 기능을 더 쉽게 할 수 있게 된 것이다! (state가 바뀌면, React가 컴포넌트를 리렌더링 하는 과정을 더 쉽게 설계가능.)

추가적으로 React.useState()를 통한 렌더링 과정에서 리액트의 장점 중 하나를 찾아볼 수 있다. useState를 사용해서 함수로 기존 state를 바꿀 때, 컴포넌트 전체가 재생성 될 것이다. 전체 코드가 다시 실행될 것이지만, React.js는 리렌더링될 때 두번째 이벤트 리스너가 등록되거나 Total click을 다시 만드는 것이 아니라, 오로지 counter 부분만 바뀌게 해준다. 

### 사용자들의 input을 어떻게 얻을까(State Funtions)

form을 만들었을 때 state는 어떤 식으로 작용할까?
이것을 탐구하기 위해 우선 앱의 state를 바꾸는 법에 대해서 알아보자. 

나는 지금 
const [counter, setCounter] = React.useState(0)을 통해 counter의 현재 값을 가져오고 있고, 그리고 counter를 수정하기 위한 함수도 불러오고 있다. 또한, onClick 함수에서 counter + 1로 수정해주고 있다. 
즉 여기 counter 라고 설정한 현재 state를 이용해서 다음 state를 계산해주고 있는 것이다. 

하지만, 이런 방식이 무조건 좋지는 않다. counter가 다른 곳에서도 update 될 수 있기 때문이다. 

state를 바꾸는 방법에는 2가지가 있다. 하나는 setCounter에 값을 직접 넣어 변경하는 방법이고, 다른 하나는 이전 값을 이용하여 현재 값을 계산해 내는 것이고 지금 내가 사용하던 방식이었다.  

만약 내가 이전 값을 바탕으로 현재 값을 설정하고 싶다면, setCounter(counter + 1); 부분을 다르게 해줄 수 있을 것 같다. 
바로 setCounter에 함수를 넣어주는 것이다. 
이 함수의 첫번째 argument는 현재 값이다. 그리고 이 함수의 return 값이 새로운 state가 되는 것이다.
setCounter((current)=> current + 1); 
사실 위 코드와 같은 기능을 수행하지만, 아래의 코드가 더 안전하게 기능을 수행할 수 있게 된다. 왜냐하면 내가 이런 식으로 함수를 사용했을 때 리액트가 내가 정확히 원하는 값으로 계산할 수 있도록 이 current가 명확하게 현재 값이라는 것을 보장하기 때문이다. 

-> 현재 state를 기반으로 다음 state를 계산하고 싶다면 함수를 이용하자!

이제 다시 input에 대한 얘기를 해보자.

단위 변환기 앱을 예시로 다뤄보자. 
아마 단위 변환기를 위해 label과 input 각각 두개를 만들 때 우리는 html에서 이런 식으로 작성을 할 것이다.
<label for="minutes">Minutes</label> 
<input id="minutes" placeholder="Minutes" type="number"/>
<label for="hours">Hours</label> 
<input id="hours" placeholder="Hours" type="number"/>

React JS 세계, 즉 JSX에서는 다르게 표현해줘야 한다.
JSX는 HTML과 비슷하지만, 몇가지 기억해야 할 게 있다.
예를 들면, class가 아닌 className을 써야 한다거나, label에서 for이 아닌 htmlFor을 써야 된다는 것 등이 있다. 이러한 규칙들을 알고 JSX를 쓰는 것은 중요하다.

이제, miniutes에 필요한 state를 만들어보자. react code로 miniutes에 어떤 숫자를 적었는지 추적할 수 있다. 
그런데, input에 적힌 value를 어떻게 얻을 수 있을까? 
React JS 세계에서는 input은 uncontrolled 라고 알려져 있다. input의 value는 우리가 통제할 수 없다는 것이다. 따라서 value를 얻기 위해 state를 만들자. 

const [miniutes, setMinutes] = React.useState()
이전에도 공부했듯이 useState는 array를 제공하고, 그 첫번째 element가 현재의 값이 되며 다음 element가 그것을 수정해주는 함수가 된다는 것을 기억하자.
나는 이 state를 다음과 같이 활용함으로써 이제 input의 value를 가져올 수 있다.
<input value={mininutes} id="minutes" placeholder="Minutes" type="number"/>

다음으로 해야할 것은 사용자가 다른 값을 입력할 때 이 value를 업데이트 시키는 것이다. 그 이벤트는 change이다.
그리고 이 onChange 이벤트에 대응하여 값을 바꿔줄 함수를 정의하자. 여기서 명심해야 할 것은 함수의 매개변수를 통해 input의 값을 불러올 때는 event.tartget.value를 사용한다는 점이다. 이를 통해 기존에 JS에서 했던 document.get ~~~ 같은 코드를 따로 짤 필요가 없다. 이것이 바로 리액트에서 input을 다루는 방법이다.
const onChange (event)=>{
    setState(event.target.value)
}
<input value={mininutes} id="minutes" placeholder="Minutes" type="number" onChange={onChange} />

onChange 함수의 주요 포인트는 데이터를 업데이트 하는 것이었다. 예를 들어 onChange를 input에서 삭제하면 현재 지정한 값은 남아있지만, input에 무언가 입력해서 바꿀 수 없다. 그 이유는 Input의 value가 state이고, 이 state의 default 값이 0이기 때문이다. 따라서 onChange 이벤트를 지우면 이 input이 키보드 이벤트를 감지한다고 해도 업데이트가 이루어지지 않는 것이다. 
<input value={Math.round(minutes / 60)}  id="hours" placeholder="Hours" type="number" />

따라서 이렇게 시간 input에는 value만 지정하고, onChange를 따로 지정하지 않으면 분에 따른 시간 변환만 가능하게 만들 수 있다. 또한, 
const reset = () => setMinutes(0)
<button onClick={reset}> reset</button>
이렇게 reset을 통해 값을 초기화할 수 있도록 했다. 
그런데, 시를 분으로 바꾸기 위해서는 어떻게 해야할까? 
onChange를 그대로 활용하기는 그렇고... 이를 위해서 바로 disabled를 활용하여 flip 함수를 만들어보자.

### 던위 변환을 뒤집어보는(flip function)

<button onClick={onFlip}>Flipped</button>

아직 우리는 minute에 대한 하나의 state만 갖고 있다. 
양측 간 변경을 위해 state를 하나 더 선언해주자.
const [flipped, setFlipped] = React.useState(false);
그리고 Filpped 버튼을 클릭했을 때 flip 상태를 true 또는 false로 바꾸어 disabled 또는 enable 상태를 취할 수 있게 onFilp 함수를 선언해준다.
const onFilp = () => setFilpped(!filpped);

이를 통해 여기 flipped가 true라면 부정명제(!flipped)는 false가 될 것이다. 반대도 마찬가지로 작동할 것이다.
하지만 일전에도 그랬듯이 다음 state의 계산을 현재 state 자체 바탕으로 하는 것은 좋지 않은 방식이었다. 
그래서 const onFlip = () => setFlipped((current) => !current); 이렇게 현재 state 값을 매개변수로 넣어주고, 결과는 그 반대로 도출하라고 명령을 넣어주면 된다! 

이제 disabled 속성에 방금 만든 flip을 활용하자!
위에서 설정한 flip의 기본 값은 false이다. 이 말은 즉 filped가 false이면, 내 Hours는 disabled되어야 한다는 것이다. 반대로 minutes의 input은 fliped가 true일 때 disabled 되어야 한다.

이를 코드에 적용하면,
 <input value={minutes} onChange={onChange} id="minutes" placeholder="Minutes" type="number" disabled={flipped} />
 <input value={Math.round(minutes / 60)}  id="hours" placeholder="Hours" type="number" disabled={!flipped} /> 이렇게 구현가능하다.
 아주 잘 작동하는 것을 확인할 수 있다! 

이제 Hours 값에 따라 Mininute으로 변환하는 기능도 넣어보자. 그런데, 문제가 하나 있다..
<input value={Math.round(minutes / 60)}  id="hours" placeholder="Hours" type="number" disabled={!flipped} /> 여기서 value 값을 minutes에서 값을 넣었을 때 60으로 나눈 값으로 설정했기 때문에 내가 Miniutes에 값을 썼을 때만 단위 변환이 일어나도록 바꿔줘야 한다. 

이를 위해 삼항연산자를 이용해보자.
value = {fliped ? minutes: Math.round(minutes/60)}
이를 통해 만약 fliped 상태라면 state에 있는 값을 보여주고, 아니라면 변환된 값을 보여주게 된다.
이를 분에도 똑같이 적용하면, value={flipped ?60*amount :amount} 이렇게 적용할 수 있다. (state를 minute input 한 곳에서만 사용하지 않기 때문에 minutes-> amount, setMinutes->setAmount로 명 변경함.)
이제 분에서도, 시에서도 모두 단위 변환이 가능해진 것을 확인할 수 있고, onFlip () 함수에 다음과 같이 reset()을 추가하여 flip 될 때마다 값을 초기화하고 변환할 수 있도록 하였다. 
const onFlip () => {
    reset()
    setFlipped((current)=> !current)
}

단위 변환기 앱을 이렇게
function App(){
    
            return (
                <div>
                    <h1>Super Converter</h1>
                    <MinutesToHours />
                    <KmToMiles />
                </div>
            )
        }
세개의 컴포넌트로 나누어 분할 정복을 했다. 
첫번째로 App 컴포넌트는 root div를 그려주는 역할을 한다. 그리고 안에 다른 2개의 컴포넌트를 렌더링하고 있다. 하나는 KmToHours로 그냥 h3 하나가 있고, 마지막 하나는 MinutesToHours인데, 우리가 이전에 작업했던 코드들이 모두 들어있다. 

이제 App 컴포넌트에 state를 가지도록 할 것인데, 이 state로 어떤 변환기를 보고 싶은지 체크할 것이다. 
const [index, setIndex] = React.useState(0)

이제 select를 만들어 줄 건데 select는 그저 Html에 속한 녀석이다. 
<select>
    <option>Minutes & Hours</option>
    <option>Km & Miles</option>
</select>

function App(){
            const [index, setIndex] = React.useState(0)
            const onSelect = (event) =>{
                setIndex(event.target.value)
            }
            return (
                <div>
                    <h1>Super Converter</h1>
                    <select value={index} onChange={onSelect}>
                        <option value="0">Minutes & Hours</option>
                        <option value="1">Km & Miles</option>
                    </select>
                </div>
            )
        }
이렇게 작성하여 input과 같은 방식으로 value의 현재 데이터를 가져오고 확인할 수 있다. 이제 index 값을 바탕으로 MinutesToHours를 쓸지, KmTomMiles를 쓸지 결정해야 한다. 그러나 여기서는 if 문을 쓸 수 없다. JSX에서 if 문을 쓰면 text로 인식될 것이다. 
그래서 {} 안에 JS를 쓴다. 
{index === "0" ?<MinutesToHours /> : null}
{index === "1" ?<KmToMiles /> : null}
이렇게 삼항연산자를 이용하여 index의 변화를 리스닝해주었다. 
이제 select option 항목의 선택을 통해 index가 0이냐 1이냐에 따라 다른 컴포넌트를 화면에서 출력하게 된다.
최종코드(
        function MinutesToHours(){
            const [amount, setAmount] = React.useState(0);
            const [flipped, setFlipped] = React.useState(false)
            const onChange = (event) =>{
                setAmount(event.target.value);
            }
            const onFlip = () => {
                reset()
                setFlipped((current)=>!current)};
            const reset = () => setAmount(0);
            return (
                <div>
                    <div>
                        <label htmlFor="minutes">Minutes</label> 
                    <input value={flipped ?60*amount :amount} onChange={onChange} id="minutes" placeholder="Minutes" type="number" disabled={flipped} />
                    </div>
                    <div>
                        <label htmlFor="hours">Hours</label> 
                    <input value={flipped ?amount :Math.round(amount / 60)} onChange={onChange} id="hours" placeholder="Hours" type="number" disabled={!flipped} />
                    </div>
                    <button onClick={reset}>Reset</button>
                    <button onClick={onFlip}>{flipped ? "Turn back" : "Invert"}</button>
                </div>
            )
        }
        function KmToMiles(){
            return <h3>KM 2 M</h3>
        }
        function App(){
            const [index, setIndex] = React.useState("xx")
            const onSelect = (event) =>{
                setIndex(event.target.value)
            }
            return (
                <div>
                    <h1>Super Converter</h1>
                    <select value={index} onChange={onSelect}>
                        <option value="xx">Select your units</option>
                        <option value="0">Minutes & Hours</option>
                        <option value="1">Km & Miles</option>
                    </select>
                    <hr />
                    {index === "xx" ? "Please select your unit" : null}
                    {index === "0" ?<MinutesToHours /> : null}
                    {index === "1" ?<KmToMiles /> : null}
                </div>
            )
        }
        const root = document.getElementById('root')
        ReactDOM.render(<App />, root);
)
### Props 
Props는 부모 컴포넌트로부터 자식 컴포넌트에 데이터를 보낼 수 있게 해주는 방법이다. 현재 위 예제에서 보면, 자식 컴포넌트인 MinutesToHours 또는 KmToMiles 두 컴포넌트는 그다지 부모 컴포넌트 (App)의 데이터를 필요로 하지 않는다. 부모 컴포넌트로부터 자식 컴포넌트로 데이터를 보내는 예시를 만들어보자. 

컴포넌트는 어떤 JSX를 반환하는 함수이다. 
function Btn(){
            return (<button style={{
                backgroundColor:"tomato",
                color:"white",
                padding:"10px 20px",
                border:0,
                borderRadius: 10,
            }}>Save Changes</button>
            );
        }
        function App(){
            return (
                <div>
                    <Btn banana="Save Change"/>
                    <Btn banana="Continue"/>    
                </div>
            )
        }
이런 식으로 Btn 컴포넌트를 만들고 부모 컴포넌트에 선언해두었다. 그런데 여기서 Btn 컴포넌트의 text 값을 App 컴포넌트 차원에서 자유롭게 바꾸고 전달하기 위해서는 어떻게 해야할까? 바로 props를 이용하면 된다.
함수 컴포넌트의 괄호 안에는 argument 받는데, 첫번째 argument 이름은 마음대로 지을 수 있다. 사람들은 이 인자 이름을 props라고 부른다. Btn으로부터 전달 받는 properties인 것이다. 
그렇기에 function Btn(props)를 추가하여 코드를 쓰면,
결국 Btn() 함수를 불러서 banana라는 인자를 
Btn({banana:"Save Changes"}) 이런 식으로 보내는 것과 같은 것이다. 그래서 어떤 prop이든 내가 Btn 컴포넌트에 보내면 그것들은 Btn 함수의 첫 번째 argument(인자) 속에 들어갈 것이다. 
React.js 가 이 부분에서 실제로 하는 작업은 Btn() 이렇게 함수를 호출해서 우리가 넣어준 모든 것들을 첫번째 인수로 보내준다. 다시 한 번 언급하면, ReactJS는 자동으로 내가 이곳에 넣는 모든 property(prop)들을 모조리 오브젝트 안에 넣어줄 것이고, 이 오브젝트(객체)는 컴포넌트의 첫 번째 인자로 주어지는 것이다. 
결론적으로 props는 이 Btn이 전달 받는 유일한 인자이다.
그리고 props는 오브젝트이다. 우리가 App에서 보낸 모든 것들을 갖는 오브젝트이다. 그래서 
<Btn banana="Save Change" x={false}/>
<Btn banana="Continue" y={7}/> 이렇게 적고 
Btn 함수형 컴포넌트 내에서 console.log(props)를 해보면, {banana: 'Save Changes', x:false} 와 같이
첫 번째 Btn의 props는 이렇게 내가 넣은 모든 것을 갖는 오브젝트가 된다.

이제 banana를 활용해보자. 오브젝트를 받고 있다는 걸 알고 있고, 이 오브젝트는 바나나라는 이름의 key를 가지고 있다.
그래서 {props.banana}를 하면 우리는 <Btn banana="Save Change"> <Btn banana="Continue"> 여기서 같은 Btn 컴포넌트를 사용하지만, 이 버튼들은 App 컴포넌트에 의해 '설정'되고 있다는 것을 알고 있다. 따라서 단 하나의 Btn 컴포넌트가 있지만, 결과가 다르게 출력되는 것이다. 다시 말해 재사용이 가능한 것이다. 사실 props.banana 이런 식으로 쓰기보다 좀 더 빠르게 자식 부모 간 전달 과정을 만들어낼 수 있다. 이 대신에 props는 오브젝트이기 때문에 function Btn() 컴포넌트의 괄호 안에 "{}" 중괄호를 열어 function Btn({banana}) 이렇게 하여 오브젝트로부터 banana를 받을 수 있다. 고로 출력하길 원할 때 {banana}라고만 입력하면 된다. 
(위 설명 전체 코드 )
<body>
    <div id="root"></div>
    <script type="text/babel">
        function Btn({banana}){
            return (<button style={{
                backgroundColor:"tomato",
                color:"white",
                padding:"10px 20px",
                border:0,
                borderRadius: 10,
            }}>{banana}</button>
            );
        }
        function App(){
            return (
                <div>
                    <Btn banana="Save Change"/>
                    <Btn banana="Continue"/>    
                </div>
            )
        }
        const root = document.getElementById('root')
        ReactDOM.render(<App />, root);
    </script>
</body>
</html>
이건 단지 shortcut을 한 것뿐이다. property를 오브젝트로부터 꺼내는 것이다. 왜냐? props는 오브젝트기 때문에 우리는 props 안에 바나나가 있다는 것을 알 수 있고, 그래서 이렇게 할 수 있는 것이다. 즉 우리의 btn 함수 컴포넌트의 첫 번째 인자인 이 오브젝트로부터 banana를 받고 있는 것이다. 
또한, prop은 더 많은 곳에서 사용할 수 있다. 예를 들어 
<Btn banana="Save Change" big="true">
<Btn banana="Continue"/> 
function Btn({banana, big}) 이런 식으로 만들고 확인하면, 첫번째 Btn의 big은 true가 될 거고, 두번째 Btn의 big은 undefined가 될 것이다.  

이 props로 전달된 속성을 바탕으로 새로운 작업을 해볼 수도 있다. 예를 들어 
<script type="text/babel">
        function Btn({banana, big}){
            return (<button style={{
                backgroundColor:"tomato",
                color:"white",
                padding:"10px 20px",
                border:0,
                borderRadius: 10,
            // 수정되는 코드(바로 아래)
                fontsize: big ? 18 : 16
            }}>{banana}</button>
            );
        }
</script>
이렇게 big이 존재한다면, fontSize는 18 아니면 16 이렇게 할 수 있다. style 안에서 if else도 사용할 수 있는 것이다.

이제 props에 뭘 보낼 수 있는지 확인해보자. 위에서 살펴봤을 때, string, true, false 등 다양한 자료형을 보낼 수 있었다. 원한다면 function도 보낼 수 있다. 

그리고 Btn을 렌더링하는 컴포넌트가 어떻게 되는지도 알아보자. 예를 들어 부모 컴포넌트가 state(상태)를 변경할 때 어떤 일이 일어나는지를 보자. 다른 컴포넌트들에게 어떤 일이 일어날까?
<script type="text/babel">
        function Btn({banana}){
            return (<button style={{
                backgroundColor:"tomato",
                color:"white",
                padding:"10px 20px",
                border:0,
                borderRadius: 10,
            }}>{banana}</button>
            );
        }
</script>
big은 지우고 다시 시작해보자. 내 Btn들에 onClick function을 달아주자. 이 onClick function은 내 App 컴포넌트에 있는 뭔가의 state를 바꾸게 될 것이다.
const [value, setValue] = React.useState("Save changes")
return (
    <div>
        <Btn text={value} />
        <Btn text="Continue">
    </div>
)
이렇게 바꿔준다. 이제 함수를 만들어보자. 
const changeValue = () => setValue("Revert Changes")
그리고 이 함수를 Btn의 onClick에 대응하는 함수로 보내준다.
<Btn text={value} onClick={changeValue}/> 여기서 사실 중요한 것은 이것들(onClick={changeValue})은 실제 EventListener가 아니고 Btn으로 들어가는 prop이다. 
만약 내가 onClick을 Button 컴포넌트의 Html요소에다가 넣는다면, 그게 바로 이벤트리스너인 것이다. 다시 한번 말하자면 내가 <Btn text={value} /> 이 커스텀 컴포넌트에 onClick을 넣는 것은 이벤트리스너가 아닌 하나의 prop을 줄 뿐이라는 것이다. 따라서 동일한 이유에서 내가 만약 이 첫 번째 Btn을 클릭한다면, 어디에도 등록된 onClick 이벤트를 볼 수 없다. 발생시키려면 기존의 prop 전달 방식처럼 손수 onClick을 전달받아와서 onClick을 설정해줘야 한다.
function Btn({text, onClick}){
    return(
        <button
            onClick={onClick}
            style={{
                backgroundColor: "tomato",
                color: "white",
                padding:"10px 20px",
                border: 0,
                borderRadius: 10,
            }}
        >
        </button>
    )
}
그래서 다시 한번 내가 커스텀 컴포넌트에 뭘 넣든지 간에 그것들은 단지 prop일 것이다. 결코 HTML태그에 자동으로 들어가지 않고 손수 해줘야 한다. 이걸 좀더 쉽게 이해하기 위해 <Btn text={value} changeValue={changeValue}/> 
function Btn({text, changeValue}){
    return(
        <button
            onClick={changeValue}
            style={{
                backgroundColor: "tomato",
                color: "white",
                padding:"10px 20px",
                border: 0,
                borderRadius: 10,
            }}
        >
        </button>
    )
}
이렇게 바꿔보면 button은 changeValue라는 onClick 리스너를 갖게 되는 것이다. 이와 같이 prop들은 직접적으로 return문 안으로 들어가지는 않지만, 커스텀 컴포넌트의 prop으로 원하는 곳에서 사용할 수 있다. 

여기서 또 다른 궁금증이 하나 있다. 어떤 부분이 Btn Continue를 다시 그리는 걸까? 우리는 바로 React Memo를 할 수 있다. 리액트에게 우리는 이 컴포넌트 다시 그려지는 것을 원하지 않는다고 말할 수 있는 것이다. 우리는 특정 컴포넌트를 다시 그릴지 아닐지를 prop이 변경되지 않는 한에서 결정할 수 있다. 
<Btn text={value} changeValue={changeValue}/> 
<Btn text="Continue">
첫번째 버튼의 props는 state와 연결되어 있기 때문에 반드시 바뀔 거다. 하지만 <Btn text="Continue"> 이 props는 절대 바뀌지 않는다. 그래서 우리는 React에게 Btn의 re-render를 멈춰달라고 할 수 있다. 이를 위해 리액트 Memo를 사용해보자. 
const MemorizedBtn = React.memo(Btn)
MemorizeBtn은 바로 memorized version의 Btn이 될 것이다. 
<Btn MemorizedBtn={value} changeValue={changeValue}/> 
<Btn MemorizedBtn="Continue">
이렇게 바꾸고 버튼을 클릭해보면, Continue는 console 창에서 다시 그려지지 않는다. 만약 부모의 state 중 어떤 변경이라도 있다면 모든 자식들은 다시 그려지게 되고, 이건 추후 내 어플리케이션이 느려지는 원인이 될 수도 있다. 만약 내가 하나의 컴포넌트를 갖는데 그것이 천 개의 컴포넌트를 그리고 있다면 더욱이 그럴 것이다. 자주 사용하는 것은 아니지만 중요한 개념이라고 할 수 있다. 

### prop Types 
컴포넌트를 다룰 때 알아야만 하는 것들 중 하나인 prop Types를 알아보자. 알다시피 props를 통해 컴포넌트의 환경을 원하는 만큼 설정할 수 있기 때문에 어떤 prop이든 보낼 수 있고 하고 있는 뭐든 기능을 가능하게 한다. 문제는 내가 컴포넌트를 만들었는데, 그 컴포넌트가 매우 많은 props를 가질 때 생긴다. 때때로 내가 또는 팀원이 잘못된 prop을 전달하는 등의 어떤 실수를 저지를지도 모른다. 한가지 예시를 살펴보자. Btn에 한 가지 더 configuration(설정)을 해보자. user가 prop으로 fontSize를 전달할 수 있도록 해볼 것이다. 
function Btn({text, fontSize})
<Btn text="Save Changes" fontSize={18}>

function Btn({text, fontSize}){
    return(
        <button
            onClick={changeValue}
            style={{
                backgroundColor: "tomato",
                color: "white",
                padding:"10px 20px",
                border: 0,
                borderRadius: 10,
                fontSize,
            }}
        >
        </button>
    )
}
새로 고침해보면 잘 작동한다. 근데 만약 내가 협업을 하고 있고, 팀원이 꼼꼼히 읽지 않거나 실수를 했다면 (예를 들어 prop에 전송하지 말아야 할 것들을 전송한다든가.. 어쩌면 text prop에 string 타입을 보내는 대신에 숫자를 넘겨줄 수도 있다. 그리고 fontSize에 text를 보내는 실수를 범할 수도 있다.) 이것을 방지할 수 있는 것이 prop Types이다. 물론 text 속성을 숫자로 fontSize를 string으로 내보내는 것이 규칙에 어긋나지는 않지만, 내가 사용하고 있는 컴포넌트 방식 내에서는 에러이기 때문에 방지해야 한다. 
이 prop Types를 통해서 React가 현재 내가 실수를 범하고 있다고 말해줄게 만들 수 있다. 나는 text가 String이 되기를 원하고, fontSize는 number가 되기를 원한다.
리액트의 prop Types 패키지는 내가 어떤 타입의 prop를 받고 있는지를 체크해준다. 
<script src="https://unpkg.com/prop-types@15.7.2/prop-types.js"></script>
패키지 url을 넣어주고 이제 React JS에게 나의 prop들의 타입이 뭔지 알려주면 된다. 
나의 컴포넌트의 이름을 가져온 뒤, props의 타입이 뭐고, 어떤 모양이어야 하는지를 설명해줄 수 있다. 
아주 간단한데, 그냥 prop 이름과 그 type을 쓰면 된다.
Btn.propTypes ={
    text: PropTypes.string,
    fontSize: PropTypes.number,
} 
이제 새로 고침을 해보면, console 창에 경고 문구가 있는 것을 확인할 수 있다. 다시 한번 말하자면, ReactJs는 UI 내에서는 어떤 에러도 보여주고 있지 않다. 하지만 지금 여기서 내가 하고 있는 것은 React에게 prop들의 type이 어떻게 되는지 설명하고 있는 것이다. 그리고 이를 통해 누군가가 실수를 했을 때 깨닫게 만들어줄 수 있는 것이다. 이와 같이 자료형에 대한 것으로 설명할 수 있지만, 공식 문서를 보면 PropTypes.oneOf(['News', 'Photos']) 와 같은 코드를 통해 이 중에 하나여야만 유효한 식으로 설정해줄 수도 있다. 또한, 필수냐 선택 사항이냐를 설정해줄 수 있는데, PropTypes.func.isRequired 이런 식으로 작성하면 반드시 function이여야 한다는 것을 전달하는 것이다. 어떤 prop들이 반드시 들어가야 하는 것으로 확실하게 설정하고 싶다면, PropTypes.자료형.isRequired 이런 식으로 작성해주면 된다. 

이번 props에 대한 학습을 통해 설정 가능한 컴포넌트를 갖는다는 것이 얼마나 편리한지 알게 되었다. 이제는 특정 컴포넌트의 내용을 만들기 위해 복붙하지 않아도 된 것이다. 그래서 복붙하는 대신에 어떤 prop들을 받을 수 있는 Btn 컴포넌트를 만들었다. prop들은 내가 인자를 사용해 컴포넌트에 데이터를 보내기 위한 통로로 활용되었다. prop들은 렌더링되고 있는 Btn 컴포넌트의 부모로부터 전달되었다. 
<Btn text="Save Change" fontSize={18}/> 
<Btn text={"Continue"} />
여기 있는 대로 Btn 컴포넌트들은 text와 fontSize 두 prop을 받고 있다. 이 Btn 컴포넌트의 이 prop들에 접근할 수 있는 방법은 이 Btn 컴포넌트 함수의 첫 번째 인자 안에서 가능하다는 사실을 알 수 있었다. 
-> function Btn({text, fontSize})
여기 첫번째 인자안에서 전달된 모든 prop들을 하나의 오브젝트로서 받는 것이다. 이 prop들에 접근하고 싶다면, propt.text 또는 props.fontSize라고 적어도 되고,
또는 오브젝트 안 요소들을 쉽게 빼올 수 있는 ES6의 손쉬운 방법을 써도 된다. 나는 ES6 방식을 통해 가져왔다. (이 방식이 짧기에 일반적으로 채택됨.) 또한 컴포넌트에서 prop의 이름은 내가 prop을 전달할 때의 이름과 받아서 사용할 때의 이름이 동일해야 한다. 

마지막으로 학습한 것은 propTypes였다. 때때로 일부 prop들을 넣는 걸 깜빡한다든지, Type을 잘못 입력한다든지 하는 실수를 방지 하기 위해 사용하는 React의 패키지였다. propTypes는 Btn이 어떤 prop들을 받는지 검사하는 게 가능하게끔 도와주었다. 

## Create React App
create-react-app은 리액트 애플리케이션을 만드는 최선의 방식이다. 이전에는 리액트 애플리케이션을 스크립트를 일일이 import해서 사용했지만, create-react-app을 통해 애플리케이션을 만들면 더 쉽고 덜 번거로울 것이다. 왜냐하면 공식 문서를 보면 create-react-app은 필요한 많은 스크립트와 많은 사진 설정들을 준비 및 제공해주기 때문이다.

내가 앞으로 react 앱을 만들 때 create-react-app을 사용한다면, 개발 서버에 접근한다든가, 자동으로 새로고침 해주거나, 즉각적으로 애플리케이션 내에 CSS를 포함시켜주는 것을 제공해준다. 또한 내가 웹사이트를 publish할 준비가 되었다면, create-react-app은 publish하는 명령어를 가지고 있다. 

npm create-react-app my-app 을 뭘 해야만하는지 살펴보자. 우선 node.js를 설치해야 한다. 자신의 컴퓨터에 node가 설치되어 있는지 확인해보려면, 명령 프롬프트 창에 node -v를 입력하면 된다. 확인할 게 하나 더 있는데, npx install 이 되는지 확인해보아야 한다.  만약 이 npx를 실행시킬 수 있다면, 준비가 된 것이다. 

이제 프로젝트를 만들어보자. 명령 프롬프트에 다음과 같이 입력해본다. npx create-react-app react-review-project

설치가 끝나면 vs code에서 폴더를 열어 package.json을 확인한다. 
프로젝트를 열어보자. code react-review-project
package.json을 들어가보면 실행시킬 수 있는 script들이 있는 걸 확인할 수 있다. 여기에 있는 start 스크립트를 실행해보자. npm start
그럼 개발용 서버를 만들게 되고, 다음과 같은 페이지를 얻게 된다. 
src 폴더는 많은 것을 가지고 있다. 그 중 무엇보다 중요한 것은 바로 index.js이다. index.html과 index.js를 보면 이전에 내가 했었던 작업들이 되어 있다. (div를 만들고 id를 연결해서 reactdom에서 render 하는..) 이제는 손수 설정할 필요가 없어졌다. 
그리고 create-react-app을 통해 얻을 수 있는 또 다른 장점은 Auto-Reload(자동 재실행)이다. 예를 들어 App.js에서 p 태그 안에 텍스트를 Hello!로 바꾸고 저장하면 Hello!로 업데이트 된 것을 확인할 수 있다. 

이제 본격적인 프로젝트 시작을 위해 필요없는 코드들을 지워보자. index.js에서는 
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
요것만 남게 지워준다. 이 세 import와 ReactDom.render만 남겨두자. App.js에서도 필요없는 것을 지워준다. CSS와 logo를 지우고 그냥 div와 h1만 남겨두자.
function App() {
  return (
    <div><h1>Welcome back!</h1></div>
  );
}

export default App;
이건 단지 컴포넌트이다. 하지만 지금 나는 node.js로 작업하고 있기 때문에 파일들을 각각 분리시키는 게 가능하고, 좀 더 조직적으로 구성할 수 있다. 한 파일당 한 컴포넌트를 가지고 있고, index.js에서 그 컴포넌트를 import 시켰다. 즉 차후에 버튼과 같은 다른 컴포넌트도 한개의 파일로 이루어질 것이라는 걸 의미한다. 그래서 보다시피 create-react-app은 꽤 많은 기본 파일들이 딸려 있다. 모두 지워주자. (App.test.js, App.css, index.css, logo.svg, reportWebVitals.js, setupTests.js 등등..) App.js와 index.js를 제외하고 모두 지웠다. 여기 우리 리액트 앱을 렌더링해줄 index와 Welcome back!을 렌더링해줄 뿐인 우리의 App을 남겨두었다. 
그리고 화면을 보면, Welcome back!이 있는 것을 확인할 수 있다. 

이제 컴포넌트도 만들고, props도 좀 넣고, propTypes도 확인해보자. 새로운 파일을 만들어보자. Button.js
function Button({text}){
    return <button>{text}</button>
}
export default Button; -> App.js에서 Button을 가져오려면 이 코드를 반드시 해줘야 한다. 
App.js 파일에서 import Button from "./Button";
을 해주고 사용해보자.(사용을 하지 않으면, import 했지만 사용하지 않았다고 터미널에 경고가 뜬다.)

import Button from "./Button";

function App() {
  return (
    <div>
      <h1>Welcome back!</h1>
      <!--수정된 코드-->
      <Button />
    </div>
  );
}
export default App;
이렇게 컴포넌트를 넣어준다. 이제 이전에 했던 것처럼 text를 prop으로서 전달해보자. 
<Button text={"Continue"} />
예시로 이렇게 설정하고 확인하면 잘 전달된 것을 확인할 수 있다. 이번에는 PropTypes를 체크해보자. 
PropTypes를 install 하자. npm i prop-types
이제 Button.js에서 propTypes를 import 해보자.
import propTypes from "prop-types";

function Button({text}){
    return <button>{text}</button>
}
Button.propTypes = {
    text:propTypes.string.isRequired
}

export default Button;

이전에 했던 거랑 코드도 같고, PropType도 같다. 그리고 props를 받아내는 방법도 똑같다 하지만, 이번에는 서로 다른 파일들로 코드를 분할하는 등의 작업을 할 수 있게 되었다. 또한 react-create-app을 통해 작업할 때 우리는 두 가지의 CSS 옵션을 가질 수 있다. 한가지 옵션은 하나의 CSS 파일을 가지는 것이다. styles.css라고 부르자.
styles.css
button{
    color:white;
    background-color: tomato;
}

index.js에 import 해준다
import "./styles.css";

이것도 괜찮고, 이 방법은 아주 편하다. 단지 css를 작성하고 import 해서 css를 작동하게 하는 방식이다. 하지만, css 파일을 import 하지않고 쓰는 방법도 있다. 
예를 들어 새로운 버튼을 만들었는데 그 버튼의 배경색으로는 tomato를 원하지 않을 때 styles.css를 사용하지 않을 거다. 그래서 다른 옵션으로 이전에 시도해봤던 또 다른 방식인 style prop이다.
function Button({text}){
    return <button style={{
        backgroundColor:"tomato",
        color:"white",
    }}>{text}</button>
}
또 만약 내가 global(전역적인) CSS style을 원하지 않는다면, 하지만 이렇게 style을 직접 넣고 싶지 않다면(index.js에 css를 import 하면 css 파일은 페이지의 모든 것들에 적용되기에), css modules을 활용하면 된다!

styles.css의 이름을 Button.module.css로 바꾼다. Button.module.css 내에서 btn이라는 클래스를 만들어 보자. 그리고 알다시피 Button.module.css를 index.js에 import 하지 않고 대신에 이 스타일을 Button.js에 import 시켜준다. 
Button.js
import propTypes from "prop-types";
import styles from "./Button.module.css";

function Button({text}){
    return <button >{text}</button>
}
Button.propTypes = {
    text:propTypes.string.isRequired
}

export default Button;
이것이 편리한 이유는 class 이름을 내 버튼에 추가할 수 있는데, return <button className={styles.btn}>{text}</button> 그냥 btn이 아닌 이런 식으로 쓴다. 
create-react-app은 CSS 코드를 JS 오브젝트로 변환시켜준다. 그리고 이 JS 오브젝트는 btn을 안에 갖고 있는 것이다. 고로 이제 동일한 class 이름을 btn을 다른 파일 내에서도 사용할 수 있다. 
이렇게 CSS를 모듈로 사용하는 것은 개발 내내 다른 클래스 이름들을 사용하기 위해 기억하고 있어야만 하는 것보다 훨씬 편하다. 이제는 다른 클래스 이름을 기억할 필요가 없다. 이제 나는 컴포넌트를 분리해서 만들 수 있고 컴포넌트를 위한 CSS를 분리해서 만들 수 있다. 

### effect
App.js
import {useState} from "react";

function App() {
  const [counter, setValue] = useState(0);
  return (
    <div>
      <h1>Welcome</h1>
    </div>
  );
}

export default App;
useState가 반환 값으로 array를 줄 거라는 걸 안다. 
그 array는 여기 있는 value와 두번째로 써준 modifier function이 될 것이다. 이번에는 onClick function을 만들어보자. 
const onClick = () => setValue((prev) => prev + 1);
그리고 이번에는 click me 라고 써있는 버튼을 만들자.
그리고 이제 welcome 대신에 counter를 넣어준다.
App.js
import {useState} from "react";

function App() {
  const [counter, setValue] = useState(0);
  return (
    <div>
      <h1>{counter}</h1>
      <button onClick={onClick}>click me </button>
    </div>
  );
}
export default App;
지금 내가 한 작업은 이전에 했던 익숙한 작업들일 뿐이다.
value를 받아오고, value를 수정하기 위해 function을 받아오고 이전 state를 받아서 +1값을 리턴하는 함수를 만들었고, 그 값을 h1을 통해 보여줬으며, 우리가 state를 modify할 수 있는 button을 만들었을 뿐이다. 
하지만 이제 해결해야 하는 문제가 하나 있다. 가끔은 계속 다시 render 될 때마다 반복실행되어도 괜찮을 코드가 있지만 가끔은 그렇게 하지 않고, component가 처음 render 될 때만 코드가 실행되길 원할 수 있다. 

예를 들어
function App() {
  const [counter, setValue] = useState(0);
  return (
    <div>
      <h1>{counter}</h1>
      console.log("render");
      <button onClick={onClick}>click me </button>
    </div>
  );
}
지금은 내 state를 변화시킬 때마다 매번 이 console.log는 실행될 것이다. 그걸 원하지 않을 때가 있을 수 있다. 처음에만 render되고 그 뒤에는 되지 않기를 원하는 경우는 언제일가? 예를 들어 내가 API를 통해 데이터를 가져올 때 첫번째 component render에서 API를 call하고
이후에 state가 변화할 때 그 API에서 데이터를 또 다시 가져오고 싶지는 않을 것이다. 그래서 기존에 방식대로라면 state가 변화할 때마다 API를 계속해서 다시 불러올 것이고, 이 과정은 불필요할 것이다. 어떻게 특정 코드들이 첫번재 component render에서만 실행되게 할 수 있을까. 그 코드가 바로 useEffect()이다.

useEffect는 두 개의 argument를 가지는 function이다.
첫번째 argument는 딱 한번만 실행하고 싶은 코드가 될 거다. 두번째 argument는 나중에 다시 이야기하자^^
useEffect에는 component가 처음 render할 때 실행되고 다시는 실행되지 않을 function을 넣어주자.
function App() {
  const [counter, setValue] = useState(0);
  return (
    <div>
      <h1>{counter}</h1>
      console.log("I run all the time");
      const iRunOnlyOnce = () =>{
          console.log("i run only once.");
      }
      useEffect(iRunOnlyOnce, []);
      <button onClick={onClick}>click me </button>
    </div>
  );
}
이렇게 하고 새로고침을 하면 console에 
I run all the time
iRunOnlyOnce 
둘 다 찍히는 것을 확인할 수 있고, 이번에는 click me를 눌러 state를 변경한 후의 console을 봐보자. 
이처럼 (사진 두개 첨부)I run all the time는 state가 변할 때마다 매번 실행되는 반면 i run only once는 한번만 출력된다. 결론적으로 useEffect function은 쉽게 말해 지정한 코드가 딱 한번만 실행될 수 있도록 보호해주는 것이다. 
이제 나는 reactJS가 나에게 언제 코드를 실행할지 안할지 결정할 tool을 제공한다는 것을 알게 되었다. 
이번에는 다른 문제를 찾아보면서 위에서 설명하지 못한 두번째 인수에 대한 설명을 살펴보자. 
search bar 하나를 만들어보자.
App.js
import {useEffect, useState} from "react";

function App() {
  const [counter, setValue] = useState(0);
  const onClick = () => setValue((prev) => prev + 1);
  console.log('i run all the time')
  useEffect(()=>{
    console.log("CALL THE API...")
  }, []);
  return (
    <div>
      <input type="text" placeholder="Search here..."></input>
      <h1>{counter}</h1>
      <button onClick={onClick}>click me</button>
    </div>
  );
}

export default App;
이제 저번에 했던 state와 input 콤비네이션을 연습해보자.
그러려면 state를 하나 더 만들어줘야 했었다. 그리고 onChange라는 function을 하나 만들어주자. 
-> const [keyword, setKeyword] = useState("") 추가함
import {useEffect, useState} from "react";

function App() {
  const [counter, setValue] = useState(0);
  const [keyword, setKeyword] = useState("")
  const onClick = () => setValue((prev) => prev + 1);
  const onChange = (event) =>setKeyword(event.target.value);
  console.log('i run all the time')
  useEffect(()=>{
    console.log("CALL THE API...")
  }, []);
  return (
    <div>
      <input value={keyword} onChange={onChange} type="text" placeholder="Search here..."></input>
      <h1>{counter}</h1>
      <button onClick={onClick}>click me</button>
    </div>
  );
}

export default App;

작업 설명-> 그냥 input을 만들어서 event listener와 연결했고, onChange function이 작동할 때 이벤트를 발생시킨 input에서 value를 받아서 그 value를 ketword에 넣어주었다. 그리고 그 keyword를 가져와서 input의 value로 사용하면 우리가 원할 때 이 input을 조작할 수 있는 것이다. 이제 새로고침을 해서 console을 보면 input에 값을 입력할 때마다 state가 modify되어서 다른 코드들도 함께 다시 실행되는 것을 확인할 수 있다. (useEffect3)  여기서 useEffect를 써야 하는 또 다른 이유를 찾았다. 만약에 누군가 글자를 타이핑할 때마다 API를 새로 호출한다고 생각할 수 있는 것이다. 
이번에 하고 싶은건 검색이다. 내가 이 검색창에 무언가를 썼을 때, 검색 API를 이용하는 것이다. 그런데, 그렇다고해서 이 click me 버튼을 클릭하여 state가 매번 바뀔 때마다 검색 API를 호출하고 싶지는 않다. 
console.log("SEARCH FOR", keyword); 이 코드를 추가해서 console.log를 찍어보자.(useEffect 4) 보이는 바와 같이 i run all the time이 계속 실행되고, 그 다음에 search for가 실행됐다. 여기에서 나는 marvel이라고 검색하고 싶었으니까 목표는 이뤘다고 볼 수 있다. 그런데 여기에서 계속 버튼을 클릭했더니, marvel도 계속 검색이 되는 문제가 있는 것을 확인할 수 있었다. (useEffect 5)
나는 search keyword에 변화가 있을 때만 marvel 영화를 검색하고 싶은 것이다. -> counter가 변화할 때에도 marvel 영화를 검색하고 싶지는 않은 것이다. 그런데 지금은 counter 변화시에도 검색이 되고 있다. keyword가 변화할 때만 marvel을 검색하도록 하려면 어떻게 해야할까?

이제 나는 내 코드의 특정한 부분만이 변화했을 때, 원하는 코드들을 실행할 수 있는 방법을 배우고 싶다. 
즉 movie state가 변화할 때만 user가 원하는 영화를 검색하고 싶다는 것이다. 여기에서 useEffect의 두번째 인자를 배울 수 있다.
이전에 했던 것처럼 
useEffect(()=>{
    console.log("SEARCH FOR", keyword);
  }, [])
이렇게 두번째 인자로 빈 배열을 주면 첫번째 인자가 딱 한번만 실행되고 말 것이다. 그런데, 내가 keyword가 변화할 때 코드를 실행하고 싶다면 두번째 인자의 배열 안에 'keyword'를 넣어주면 된다. 여기서 한 것은 이 keyword가 변화할 때 코드를 실행할 거라고 react.js에게 알려준 것이다. 
이제 다시 결과를 확인해보면 (useEffect 6) 다음과 같이 keyword가 변화할 때만 실행된 것을 확인할 수 있다. 
이것이 바로 우리가 여기에 빈 array를 주었을 때 코드가 단 한번만 실행되는 이유이다. 빈 배열인 경우에는 react가 지켜볼 것이 아무것도 없기에 처음 한번만 실행되는 것이다. 그러나, keyword를 지정한 후에는 keyword가 변화할 때마다 코드가 항상 실행 될 것이다. 
그런데, 이 검색창은 사실 아직 완벽하게 동작한다고는 할 수 없는데, 그건 component가 시작될 때에도 검색이 되고 있기 때문이다. 그래서 이렇게 바꿔주자.
useEffect(()=>{
    if(keyword !== "" && keyword.length > 5){
        console.log("SEARCH FOR", keyword);
    }
  }, [])
수정하고 새로고침을 해보면 (6과 7사이) 다음과 같이 검색관련 console.log는 실해이 되지 않은 것을 볼 수 있고  우리가 설정한 대로 다음과 같이(7) 검색창에 marvel을 입력하고 나서야 console 창에 출력이 되는 것을 확인할 수 있다. 

다시 한번 Recap 해보면, useEffect는 두개의 argument를 가지는 function이다.
첫번째 argument는 실행시키고 싶은 코드였고, 두번째 argument는 dependencies로 react js 가 지켜보아야 하는 것이었다. 그리고 그 dependency 들이 변화할 때, react js가 코드를 실행시키는 거다. 

### Cleanup function
마지막으로 그렇게 중요하지는 않지만, 알아둬야할 useEffect의 기능에 대해 살펴보자.
App.js
import {useEffect, useState} from "react";

function App(){
  const [showing, setShowing] = useState(false);
  const onClick = () => setShowing((prev) => !prev);
  return <div>
    <button onClick={onClick}>{showing ? "Hide" :"Show"}</button>
  </div>
}

export default App;
이렇게 작성해보고, 내가 무엇을 보여주고 숨길지 한번 보자. 내가 보여주거나 숨길 것은 다른 component일 것이다. 새로운 component를 만들어주자.
function Hello(){
  return <h1>Hello</h1>
}
이렇게 함수형 component를 만들고 App 컴포넌트를
function App(){
  const [showing, setShowing] = useState(false);
  const onClick = () => setShowing((prev) => !prev);
  return <div>
    {showing ? <Hello /> : null}
    <button onClick={onClick}>{showing ? "Hide" :"Show"}</button>
  </div>
} 
다음과 같이 수정하여 showing이 True, 즉 Show를 눌렀을 때는 Hello 컴포넌트를 반환하여 Hello를 출력하고, false일 경우에는 아무것도 출력하지 않는다.

이번에는 Hello component에 useEffect를 만들자. 
useEffect(() =>{
    console.log('Im here!')
  },[])
를 추가하고 실행하면 show를 할 때마다 console에 출력이 되는데, 이유는 내가 hello component를 hide 할 때는 말 그래도 그 component를 screen에서 지워주는 것이고 show할 때는 컴포넌트가 다시 생성되는 것이다. 여기까지는 사실 Effect의 복습이다. 그런데 여기서 새로이 알 수 있는 react.js가 제공하는 것 중 하나는 component가 destroy될 때도 코드를 실행할 수 있다는 점이다. 컴포넌트가 destroy될때 이번에는 console.log()에 destroyed라고 찍어보고 싶다고 하자. 이를 위해서는 function을 return 해줘야 하는데, 이 function은 useEffect로부터 오게 된다. 해야 할 것은 단지 component가 destroy될 때 실행될 function을 하나 만드는 것이다. 
function Hello(){
  useEffect(() =>{
    console.log('created :)')
    return () => console.log('destroyed :(')
  },[])
  return <h1>Hello</h1>
}
따라서 이렇게 return 함수를 넣어주면 hide 했을 때 destroyed :( 가 console에 출력되는 것은 확인할 수 있다. 이걸 가능하게 하는 함수를 바로 Cleanup function이라고 부르는 것이다. 사실 그냥 function인데 나의 component가 destroy 될 때 뭔가 할 수 있도록 해주는 것이다. 예를 들어 누군가는 compoenent가 없어질 때 어떤 분석 결과를 보내고 싶어할 수도 있다. 그러면 그 사람들은 분석 API를 보낼 수 있을 것이다. 아니면 컴포넌트가 사라지거나 없어질 때 event listener를 지우거나 혹은 console.log 에 뭔가를 보여줄 수도 있다.
중요한 것은 이걸로 component가 언제 create 됐는지 언제 destroy 됐는지 알 수 있다는 것이다. 

function Hello(){
  function effectFn(){
    console.log("created :)")
  }
  useEffect(effectFn,[])
  return <h1>Hello</h1>
}
이 상황에서는 Hello 컴포넌트가 생성될 때에만 콘솔에 created가 출력되는 상황인데 얘기했듯이 component가 파괴될 때 function을 실행하고 싶다면, 우리의 effectFn이 새로운 function을 return 해야 한다. 
따라서 또 다른 function을 만들어주자.(이름 편의에 맞게 변경함)
function Hello(){
  function byFn(){
    console.log("bye :(")
  }
  function hiFn(){
    console.log("created :)")
  }
  useEffect(hiFn,[])
  return <h1>Hello</h1>
}
이렇게 작성하면 component가 생성될 때, hiFn이 실행될거고, 그런데 앞에서 말했던 것처럼 componenet가 언제 파괴되는지 알고 싶다면 hiFn이 byFn을 리턴해줘야 한다. 
function Hello(){
  function byFn(){
    console.log("bye :(")
  }
  function hiFn(){
    console.log("created :)")
    return byFn;
  }
  useEffect(hiFn,[])
  return <h1>Hello</h1>
}
따라서 이렇게 리턴해주자. 
다시 한번 보면, useEffect는 function을 받고, 이 function은 dependency가 변화할 때 호출된다.  지금의 경우에는 dependency가 비어있기 때문에 component가 처음 생성될 때 function이 호출된 후에 다시는 호출되지 않는다. component가 파괴될 때는 react.js는 hiFn이 return 한 function을 실행할 것이다. 
function Hello(){
  useEffect(()=>{
    console.log("hi :)");
    return ()=>{
      console.log("bye :(")
    }
  },[])
  return <h1>Hello</h1>
}
이렇게 useEffect 안에 한번에 구성되도록 코드를 줄여줄 수 있다. 

오늘은 useEffect의 사용법과 유용성을 알아보았다.
useEffect는 언제 code를 실행할지에 대한  선택권을 제공했다. 시작할 때만 code를 실행할 수 있었고, 무언가를 변화할 때 실행할 수도 있었고, 어떤 컴포넌트가 파괴될 때 실행하는 법도 배울 수 있었다.  