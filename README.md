## 2. ReAct 기반 체계를 이용한 간단한 에이전트 구현

### ReAct

**Reasoning(추론) + Acting(행동)을 반복하는 사고 방식**

1. 사고: 다음 행동을 추론하는 과정, 현재 상황을 평가하고 행동 방안 고려
2. 행동: 사고의 결과에 따라 어떤 행동을 취할지 결정
3. 관찰: 행동 실행 후 결과를 관찰하고 피드백을 수집하는 과정으로 다음 사고 방향에 영향을 미침

이러한 반복 프로세스를 통해 문제를 해결하는 에이전트 추론 엔진.

> 에이전트의 추론 엔진 - 계획 수립, 의사 결정 과정, 도구 호출을 통해 작업을 싱행하는 핵심

```python
# 전체 코드는 ch2_1.py 참조

# LangChain Hub에서 ReAct의 프롬프트 가져오기
prompt = hub.pull("hwchase17/react", api_key='LangSmith API Key')

# 사용할 대형 언어 모델 선택
llm = OpenAI()

# SerpAPIWrapper 인스턴스화
search = SerpAPIWrapper()

# 도구 목록 준비
tools = [
    Tool(
        name="Search",
        func=search.run,
        description="대형 언어 모델이 관련 지식이 없을 때 지식 검색에 사용됩니다."
    ),
]

# ReAct 에이전트 생성
agent = create_react_agent(llm, tools, prompt)

# 에이전트와 도구를 전달하여 AgentExecutor 생성
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

# AgentExecutor를 호출하여 입력 데이터 전달
print("첫 번째 실행 결과:")
agent_executor.invoke({"input": "현재 인공 지능 에이전트의 최신 연구 진전은 무엇입니까?"})
print("두 번째 실행 결과:")
agent_executor.invoke({"input": "현재 인공 지능 에이전트의 최신 연구 진전은 무엇입니까?"})
```

>**프롬프트 엔지니어링**
>- 특정한 출력을 할 수 있도록 유도하는 입력을 설계하고 최적화하는 방법

1. `hwchase` 프롬프트는 생각, 행동, 입력, 관찰을 포함하여 ReAct 방식으로 결과를 도출하도록 지시하는 프롬프트이다. 
    ```python
    prompt = hub.pull("hwchase17/react", api_key='LangSmith API Key')
    ```

2. 에이전트 추론 엔진 역할을 하는 LLM 인스턴스 생성
    ```python
    llm = OpenAI()
    ```
3. 도구 정의 - 검색 도구로 SearpAPI를 사용
   ```python
   search = SerpAPIWrapper()
   tools = [
    Tool(
        name="Search",
        func=search.run,
        description="대형 언어 모델이 관련 지식이 없을 때 지식 검색에 사용됩니다."
    ),
   ]
   ```
4. ReAct 에이전트 생성
   ```python
   agent = create_react_agent(llm, tools, prompt)
   ```
5. ReAct 에이전트 실행 - 
   ```python
   # 에이전트와 도구를 전달하여 AgentExecutor 생성
   agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)
   
   # AgentExecutor를 호출하여 입력 데이터 전달
   print("첫 번째 실행 결과:")
   agent_executor.invoke({"input": "현재 인공 지능 에이전트의 최신 연구 진전은 무엇입니까?"})
   print("두 번째 실행 결과:")
   agent_executor.invoke({"input": "현재 인공 지능 에이전트의 최신 연구 진전은 무엇입니까?"})
   ```
