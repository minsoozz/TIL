# v-shwow와 v-if/v-else 차이

## v-show

v-show 지시자는 '다른(else)' 사례가 없을 때 가장 적합하다. 조건이 참일 때 보여 줄 마크업은 있는데, 거짓일 때 보여줄 컨텐츠가 없을 때를 의미한다.

- v-show가 올바른 선택일 수 있는 사용 사례
    - 세일 발표나 이용 약관의 변화 같은 일시적인 배너
    - 사용자가 로그인 하지 않았을 때 가입이나 기타 유도 광고
    - 여러 웹 페이지에 걸쳐 있는 리스트의 페이징 요소(한 페이지에 들어갈 때는 필요 없음)

## v-if/v-else

v-if와 v-else 지시자는 마크업 덩어리 2개 중 하나가 랜더링되어야 할 때 적합하다. 하지만 둘 중 적어도 하나는 무조건 보여야 한다. 다른 사례가 없다면 v-show가 더 적합하다.

- v-if와 v-else가 올바른 선택일 수 있는 사용 사례
    - 로그아웃된 사용자에게 로그인 링크를 보여주거나 로그인된 사용자에게 로그아웃 링크를 보여줄 때
    - 사용자 선택에 따른 국가별 주소 입력란 같은 조건적인 형식 입력 섹션을 랜더링할 때. 예를 들어 미국 주소 입력은 '주(state)' 입력란을 보여주고, 캐나다 주소 입력 형식은 '지방(province)'
      입력란을 보여주는 것
