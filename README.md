# 소개 : 
이 계산기는 HTML, CSS, 그리고 TypeScript를 활용하여 만든 사칙연산이 가능한 기본 계산기 입니다.


# 실행방법 :

  1. npm 설치 : 라이브러리를 불러올 수 있도록 npm을 설치하여 줍니다.

  //npm install


  위의 코드를 터미널에 입력합니다. 그리고 세 명령에서 각각 버전이 출력되었는지 확인합니다.

  /*
  node -v
  npm -v
  git --version
  */

  


  2. npm run build 실행 :

  npm이 프로젝트에 설치된 TypeScript 컴파일러 tsc를 실행. 컴파일러는 tsconfig.json에 지정한 세 파일을 읽고 검사한 뒤, js 폴더에 JavaScript를 생성합니다.
  
  //npm run build

  다음 코드를 실행하고, 탐색기에 js 폴더가 보이고 폴더 안 하위항목에 (app.js, calculator.js, operations.js)가 생성되어 있는지 확인합니다.
  (만약 빌드에 오류가 있을 경우 새로운 javascript가 생성되지 않습니다. 이러할 시 이전 생성 파일이 남아있는지, 빌드들이 오류 없이 잘 실행되고 끝나는지 확인합니다.)


  3. index.html 실행

  계산기를 실행하기 위해, pwd-week3 폴더 안 index.html 을 더블클릭하면 브라우저가 열리게 됩니다.


# 확인결과와 파일, 함수 기록

  # 7.1 

  1. 계산담당 함수 (operations.ts)
  add﹒subtract﹒ multiply﹒divide

  2. 삭제·부호 전환·% (calculator.ts)

    - 삭제 : clear

    - 부호전환 :
    handleKey 안에있는 else if 부분에서

    else if (key === 'sign') {
        if (state.operator === null) state.expression = '';
        if (state.waiting && !state.hasOperand) {
          state.input = '0';
          state.waiting = false;
        }
        state.hasOperand = true;
        state.input = state.input.startsWith('-') ? state.input.slice(1) : '-' + state.input;
    }
    이부분에서 부호 전환이 되는 것을 확인할 수 있습니다.

    - % : 
    마찬가지로 handleKey 안에 있는 else if 부분에서

    else if (key === 'percent') {
    if (state.operator === null) state.expression = '';
    setResult(Number(state.input) / 100);
    }

    %를 담당하는 부분임을 알 수 있습니다.

  3. 쉼표표시 (app.ts)

  formatDisplay 가 담당


  # 7.2 계산식과 초기화 확인 (calculator.ts)

   1. AC -> 1 -> 2 -> + 했을 시

    '{"input":"12","stored":12,"operator":"+","waiting":true,"hasOperand":false,"error":"","expression":""}'

     이렇게 상태가 변경되며 12 + 가 표시됩니다.


   2. 3을 누를 시

     '{"input":"3","stored":12,"operator":"+","waiting":false,"hasOperand":true,"error":"","expression":""}'

     12 + 3으로 바뀝니다

   3. = 누를 시

    '{"input":"15","stored":null,"operator":null,"waiting":true,"hasOperand":true,"error":"","expression":"12 + 3 ="}'

    위쪽은 "12 + 3 =" 이 되고, 결과는 15가 도출됩니다

   4. AC를 누르면

    '{"input":"0","stored":null,"operator":null,"waiting":false,"hasOperand":false,"error":"","expression":""}'

    최종적으로 이렇게 상태가 변경되며, 계산식이 지워지고 0으로 돌아갑니다


  # 7.3 오류와 복구 확인(calculator.ts, operations.ts)

    "AC → 1 → 2 → ÷ → 0 → = "에서
    
    " = " 을 누르게 되면 divide 에서 Error가 나고, handleKey()의 catch가 받아서 state.error에 Error을 저장하고 render()가 state.error을 읽고 "Error" + 메시지 표시를 하게 됩니다.


    그리고 숫자 7을 누르면
    clear()가 input을 다시 0으로 되돌려지고, 7로 교체 됩니다. 그렇게 오류가 사라지고 7이 표시됩니다

  
  # 7.4 연속 입력과 작은 화면 확인


    <연속 입력에 관한 예시 확인한 결과>

    1. "2 + 3 × 4 =  " 결과 : 20 

    2. "2 + × 3 = " 결과 : 6

    3. "200 + 10 % = " 결과 : 200.1

    4. "2 + = " 결과 : 2
     
    5. "2 + 3 = = " 결과 : 5

    답은 예상 결과와 같게 나옵니다.

    (calculator.ts)
    연속 입력은 selectOperator()와 equals()가 처리하고, 두 함수 모두 hasOperand를 보고 계산 여부를 정하게 됩니다.


  
    <작은 화면 확인>

    (styles.css)

    1. width: min(100% - 24px, 450px)
    계산기 전체 폭을 담당합니다

    2. font-size: clamp(30px, 9vw, 56px)
    폰트의 크기를 조정합니다

    3. clamp(24px, 7vw, 42px)
    버튼 글자 크기를 조정합니다



    < Tab에 관한 확인 >

    (app.ts)
    <button>은 Enter를 치거나 스페이스바를 누르면 브라우저가 click이벤트를 만들어줘서 마우스 클릭이나 텝엔터 텝 스페이스바 다 똑같은 click처리가 됩니다.


    (calculator.ts)
    inputDigit에서
      if (state.input.replace(/[-.]/g, '').length >= 12) return;
      if (state.input === '0') state.input = key;
      else if (state.input === '-0') state.input = '-' + key;
      else state.input += key;

    부호와 소수점을 제외하고 직접 입력은 최대 12자리로 한다고 되어 있기에 그 이상으론 쳐지지 않습니다.





























