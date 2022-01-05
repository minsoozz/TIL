# mockito 정리

### 기본 어노테이션

#### @ExtendWith(MockitoExtension.class)

#### @Mock

- Mock 객체를 생성하는 어노테이션이다

#### @InjectMocks

- `@InjectMocks` 가 붙은 어노테이션 에게 `@Mock` 이 붙은 객체를 주입시킨다 

#### 기본 메소드

#### when(A).thenReturn(B)

- A가 호출이 되면 B를 리턴하라는 뜻이다