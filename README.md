# React19-Info
[React 19 공식 문서](https://react.dev/blog/2024/12/05/react-19#whats-new-in-react-19)

## Actions

데이터 변경작업을 수행한 후 상태를 업데이트. 

이전 버전에서는 state가 pending, 오류처리, 낙관적 업데이트, 순차적 요청 처리 등을 모두 수동으로 관리해야 했습니다. 

React 19버전에서는 Actions 기능을 통해 form 제출 시 발생하는 데이터 변경 작업을 자동화를 통해 간편하게 처리 가능해졌습니다.

1. 대기 상태 관리 자동화
 - 이전 버전에서는 API요청이 완료될 때까지 로딩 상태를 직접 관리해야 했지만, 최신 버전 (react19) 에서는 상태를 자동으로 인식하고 UI를 업데이트 해줍니다.

2. 오류 처리 간소화
 - 요청이 실패 시 에러 메시지를 표시하거나 상태를 복구하는 작업을 직접 구현할 필요 없이, React가 오류 상태를 감지하고 자동으로 UI에 반영합니다.
3. 낙관적 업데이트
 - 서버의 응답을 기다리지 않고 사용자가 입력한 내용이 곧바로 UI에 반영되도록 합니다. 즉 서버가 응답을 하기 전에 UI에서 이미 정보가 업데이트 된 것처럼 해줍니다.
   useOptimistic() hook을 이용하면 간단하게 가능
4. 순차적 요청 관리
 - React 19 에서는 요청 순서를 자동으로 보장하면서 상태 관리를 해줍니다.
5. Action 연결 및 상태 관리 통합
 - useActionState() hook을 통해서 onSubmit, useState 를 조합해서 사용하는 번거로움을 없앴습니다.

개발자가 직접 작성해야 헀던 코드량을 크게 줄여주고, 코드 가독성과 보수성을 높였습니다.

# 이전 버전
```
function UpdateName() {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, setIsPending] = useState(false);

  const handleSubmit = async () => {
    setIsPending(true);
    const error = await updateName(name);
    setIsPending(false);

    if (error) {
      setError(error);
      return;
    } 
    redirect("/path");
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```

# 최신 버전
```
function UpdateName() {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, startTransition] = useTransition();

  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name);

      if (error) {
        setError(error);
        return;
      } 
      redirect("/path");
    });
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}

```
<kdb> 
1. useTransiotion() hook을 사용해서 isPending 상태를 자동으로 관리하게 해줬습니다. 
2. startTransiotion() 함수 안에 비동기 로직을 넣으면, React가 로딩 상태를 자동으로 관리 합니다. 
3. setIsPending()을 직접 호출하지 않아도 React가 알아서 대기 상태를 처리합니다.
</kdb>
