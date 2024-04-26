# LangChain 개요

## 1. 대형 언어 모델 (LLM)

### 1.1. 언어 모델

**언어 모델**은 주어진 데이터 양을 기반으로 언어를 생성할 수 있는 확률 모델입니다. 그러나 실제로 그 언어를 이해하지는 않습니다.

예를 들어, 우리가 앵무새를 키운다고 가정해봅시다. 이 앵무새는 우리가 "너무 배고파, **빵** 먹고 싶어"라는 문장을 많이 말할 것입니다. 어느 날, "너무 배고파, ... "라고 말하기만 해도 앵무새는 자동으로 "빵"이라고 대답할 것입니다.

![Untitled](LangChainOverview/Untitled.png)

앵무새가 바로 **언어 모델**입니다. 매우 많은 데이터로 훈련되어 있기 때문에 우리가 입력으로 제공하는 prompt(완성되지 않은 문장)에 적합한 출력을 생성합니다. 비록 앵무새가 우리가 무엇을 말하는지는 이해하지 못하지만, 매일 우리의 말을 듣기 때문에 가장 **높은 확률**로 문장을 완성할 수 있습니다.

![LLM은 훈련된 데이터셋에서 가장 높은 확률로 나타나는 응답을 제공합니다](LangChainOverview/Untitled%201.png)

LLM은 훈련된 데이터셋에서 가장 높은 확률로 나타나는 응답을 제공합니다

### 1.2. 대형 언어 모델 (LLM)

**대형 언어 모델 (LLM)**은 딥러닝을 사용하고 매우 큰 데이터 세트로 훈련되어 정보를 이해하고 요약하고 생성하고 예측하는 알고리즘입니다.

![LLM은 DL의 일부입니다](LangChainOverview/Untitled%202.png)

LLM은 DL의 일부입니다

매일 말을 듣는 앵무새는 점점 커져서 여기저기 날아다니며 많은 사람들의 말을 듣게 됩니다. 시간이 지남에 따라 정보는 그의 뇌에서 신경망으로 연결되어 논리적으로 복잡한 질문에 대답할 수 있도록 됩니다.

이제 더 이상 단순히 문장을 완성하는 것뿐만 아니라 복잡한 질문에 대답할 수 있습니다. 이제 앵무새는 **대형 언어 모델**과 같습니다 - 매우 큰 데이터 세트로 훈련된 실체입니다. 이제 앵무새는 우리가 말하는 것을 정말로 이해합니다.

![LLM은 더 복잡한 질문을 이해하고 대답할 수 있습니다](LangChainOverview/Untitled%203.png)

LLM은 더 복잡한 질문을 이해하고 대답할 수 있습니다

다양한 데이터 세트로 훈련된 모델은 서로 다른 출력을 제공합니다.

![Untitled](LangChainOverview/Untitled%204.png)

왜 "대형" 언어 모델이라고 부르는 걸까요?

1. **매우 많은 파라미터 수** (많은 레이어와 많은 유닛으로 이루어진 신경망; 유닛 간의 연결로부터 파라미터가 생성됩니다)
   - GPT-3.5는 13억 개의 파라미터를 가지고 있습니다 (나중에 1750억으로 증가)
   - GPT-4는 1760조 개의 파라미터를 가지고 있습니다
2. **매우 큰 데이터 세트로 훈련됨**
   - GPT-3.5는 45TB의 텍스트 데이터로 훈련되었습니다 (그 양의 정보를 읽는 데 인간이 75,000년이 필요합니다)

ChatGPT를 훈련하기 위해서는 약 10,000개의 Nvidia GPU가 필요합니다

LLM은 무엇을 할 수 있나요?

- 언어 번역
- 문서 요약
- 사용자 질문에 답변
- 텍스트의 감정 분석
- 사용자의 입력과 맥락에 적합한 응답 생성

## 2. Transformer

### 2.1. 왜 Transformer를 사용하는 걸까요?

Transformer는 대형 언어 모델의 핵심 구조입니다. 그 구체적인 내용은 여기서 설명하지 않겠습니다. 너무 복잡해서요 😢

자연어 처리를 위해 **순환 신경망(RNN)**과 같은 다른 구조를 사용할 수 있지만 왜 **Transformer**를 사용해야 할까요?

- **RNN의** 구조에서는 단어가 **한 번에 하나씩** 네트워크에 입력됩니다. 따라서 데이터를 병렬로 처리할 수 없습니다. 결과적으로 학습에 많은 시간이 소요되고 GPU의 병렬 처리를 활용할 수 없으며 긴 문장의 경우 문장의 처음 단어들은 문장의 끝 단어들과 관련이 없어집니다.
  ![Untitled](LangChainOverview/Untitled%205.png)
- **Transformer**의 구조에서는 문장 전체를 **동시에** 네트워크에 입력하여 처리하고 결과로 전체 문장을 출력합니다. 하지만 Transformer를 사용할 때는 다음 두 가지를 고려해야 합니다:
  - **위치 인코딩(Positional Encoding)**: 문장의 각 단어가 어디에 있는지를 표시하여 문장의 의미를 보존합니다.
  - **Attention과 Self-Attention**: 문장 내의 단어들이 논리적으로 서로 관련되도록 돕습니다 (예: "향긋하고 파인애플이라는...").
    ![Untitled](LangChainOverview/Untitled%206.png)

### 2.2. 미세 조정(Finetuning)

대형 언어 모델은 여러 종류의 작업과 분야를 처리할 수 있습니다. 그러나 **미세 조정**은 모델을 더 효율적으로 만들어 줍니다.

**미세 조정**은 사전 훈련된 모델을 가져와서 보다 구체적인 특정 분야의 데이터 세트를 기반으로 모델을 추가 학습시키는 것을 의미합니다 (이 데이터 세트는 보통 사전 훈련된 모델의 데이터 세트보다 훨씬 작지만 특정 분야에 대해 더 깊이 있게 학습됩니다).

![Untitled](LangChainOverview/Untitled%207.png)

## 3. LangChain

### 3.1. 소개

**LangChain**은 LLM을 활용한 애플리케이션 개발을 지원하는 프레임워크입니다.

이를 통해 애플리케이션은 다음과 같은 기능을 수행할 수 있습니다:

- **논리 추론**: LLM을 활용하여 논리적인 답변을 생성합니다.
- **문맥 인식**: 질문의 문맥을 이해하여 적절한 답변을 제공합니다.
- **외부 지식과 LLM 연결**: 추가 정보(텍스트, PDF, 이미지 등)를 LLM에 제공하여 최상의 답변을 얻습니다.

이 프레임워크는 여러 구성 요소로 구성됩니다:

- **LangChain Libraries**: Python 및 JavaScript를 지원하며 다음을 제공합니다:
  - 다양한 컴포넌트(text splitter, prompt template 등)에 대한 인터페이스 및 통합
  - 이러한 컴포넌트를 체인 및 에이전트로 결합하는 간단한 런타임
  - 체인 및 에이전트의 사전 구성된 구현
- **[LangChain Templates](https://python.langchain.com/docs/templates)**: 다양한 유형의 작업에 대해 쉽게 구현할 수 있는 참조 구조의 모음입니다. (Python 전용)
- **[LangServe](https://python.langchain.com/docs/langserve)**: LangChain 체인을 REST API로 배포하는 데 사용되는 라이브러리입니다. (Python 전용)
- **[LangSmith](https://smith.langchain.com/)**: LLM 프레임워크에서 구축된 체인을 디버그, 테스트, 평가 및 모니터링할 수 있는 독립적인 프로그래밍 환경입니다. LangChain과 원활하게 통합됩니다.

![Untitled](LangChainOverview/Untitled%208.png)

### 3.2. LangChain의 작동 흐름

![Untitled](LangChainOverview/Untitled%209.png)

- 데이터 준비: PDF 파일, 웹 사이트 등을 준비하여 분할하고 특징 벡터를 추출하여 벡터 DB에 넣습니다.
  - 벡터 DB에는 다양한 유형이 있습니다. Faiss, ChromaDB 등을 사용할 수 있습니다. 이 DB에 대한 색인을 생성하여 검색을 용이하게 할 수 있습니다.
- 사용자가 쿼리를 입력하면 해당 쿼리를 Prompt Template(체인)에 넣고 임베딩 모델을 통해 특징 벡터를 추출한 후 쿼리 DB에 넣습니다.
- 쿼리 DB는 벡터 DB와 비교하여 관련 문서를 선택한 다음 관련 문서와 프롬프트를 LLM에 보냅니다.
- LLM은 이를 기반으로 응답을 생성합니다.

### 3.3. LangChain의 기본 구성 요소

LangChain의 기본 구성 요소에는 다음이 포함됩니다:

- **Model**: 사용자에게 답변을 생성하는 LLM입니다.
- **Embedding model - Retrieval model**: 사용자의 질문에서 특징을 추출하는 LLM입니다.
- **Chain**: 각각의 구체적인 작업을 수행하고 그 결과를 다음 체인의 입력으로 전달하는 체인의 연속입니다.
- **Prompt template**: 쉽게 프롬프트를 생성할 수 있도록 도와주는 템플릿입니다.
- **Output parser**: 출력의 형식을 정의하는 데 사용됩니다.
- **Document loader**: 파일, 웹 사이트 등에서 정보를 읽어오는 데 사용됩니다.
- **Vector - document**: 특정 정보의 특징입니다.
- **Vectorstore - Vector DB**: 다수의 벡터로 구성된 집합입니다.
- **Text splitter**: 모델의 토큰 제한 때문에 문서를 더 작은 부분으로 분할하는 데 사용됩니다.
- **Retriever**: 질문의 특징을 고려하고 모델에 전달하기 위해 관련 문서의 특징을 선택합니다.
- **Agent**: 웹에서 추가 정보를 검색해야 하는지 여부를 결정하는 도구입니다.

### 3.4. LLM Chain으로 실습하기

설치:

```bash
npm install @langchain/openai
```

환경 변수 설정:

```bash
OPENAI_API_KEY="..."
```

다음은 LangChain을 사용하여 간단한 메시지를 입력으로 하는 예제입니다.

```jsx
import { ChatOpenAI } from "@langchain/openai";

const chatModel = new ChatOpenAI({
  model: "gpt-3.5-turbo",
  apiKey: process.env.OPENAI_API_KEY,
});

const data = await chatModel.invoke("what is LangSmith?");
console.log(data);
```

```json
AIMessage {
  content: 'LangSmith refers to the combination of two surnames, Lang and Smith. It is most commonly used as a fictional or hypothetical name for a person or a company. This term may also refer to specific individuals or entities named LangSmith in certain contexts.',
  additional_kwargs: { function_call: undefined, tool_calls: undefined }
}
```

**피드백**: 이 예제의 결과는 우리가 원하는 것과 관련이 없습니다. 그 이유는 이 예제가 우리의 기술적 맥락을 이해하지 못하기 때문입니다.

**prompt template**을 사용하여 LangChain을 사용해 봅시다. **prompt template**을 사용하면 사용자 입력을 더 나은 형태로 변환하여 LLM에 제공할 수 있습니다.

prompt template을 사용하면 **채팅 모델**에 **맥락**을 제공하여 더 적합한 응답을 얻을 수 있습니다.

```jsx
import { ChatOpenAI } from "@langchain/openai";
import { ChatPromptTemplate } from "@langchain/core/prompts";

const chatModel = new ChatOpenAI({
  model: "gpt-3.5-turbo",
  apiKey: process.env.OPENAI_API_KEY,
});

const prompt = ChatPromptTemplate.fromMessages([
  ["system", "You are a world class technical documentation writer."],
  ["user", "{input}"],
]);

const chain = prompt.pipe(chatModel);

const data = await chain.invoke({
  input: "what is LangSmith?",
});
console.log(data);
```

```json
AIMessage {
  content: 'LangSmith is a powerful programming language created for high-performance software development. It is designed to be efficient, intuitive, and capable of handling complex computations and data manipulations. With its extensive set of features and libraries, LangSmith provides developers with the tools necessary to build robust and scalable applications.\n' +
    '\n' +
    'Some key features of LangSmith include:\n' +
    '\n' +
    '1. Strong typing: LangSmith enforces type safety, preventing common programming errors and ensuring code reliability.\n' +
    '\n' +
    '2. Advanced memory management: The language provides built-in memory management mechanisms, such as automatic garbage collection, to optimize memory usage and reduce the risk of memory leaks.\n' +
    ... +
    'Overall, LangSmith aims to provide a robust and efficient development environment for creating software applications across various domains, from scientific simulations to web development and beyond.',
  additional_kwargs: { function_call: undefined, tool_calls: undefined }
}
```

**피드백**: 이 예제의 결과는 여전히 정확하지 않지만 기술적인 측면을 갖고 있습니다.

ChatModel의 출력은 현재 메시지 객체입니다. 그러나 문자열과 작업하는 것이 더 쉽습니다. 따라서 메시지를 문자열로 변환하기 위해 간단한 **output parser**를 추가하겠습니다.

```jsx
import { ChatOpenAI } from "@langchain/openai";
import { ChatPromptTemplate } from "@langchain/core/prompts";
import { StringOutputParser } from "@langchain/core/output_parsers";

const outputParser = new StringOutputParser();

const chatModel = new ChatOpenAI({
  model: "gpt-3.5-turbo",
  apiKey: process.env.OPENAI_API_KEY,
});

const prompt = ChatPromptTemplate.fromMessages([
  ["system", "You are a world class technical documentation writer."],
  ["user", "{input}"],
]);

const llmChain = prompt.pipe(chatModel).pipe(outputParser);

const data = await llmChain.invoke({
  input: "what is LangSmith?",
});

console.log(data);
```

```json
LangSmith is a sophisticated online language translation tool. It leverages artificial intelligence and machine learning algorithms to provide accurate and efficient translation services across multiple languages. Whether it's translating documents, websites, or text snippets, LangSmith offers a seamless, user-friendly experience while maintaining the integrity and nuances of the original content. Its advanced features include context-aware translations, language customization options, and quality assurance checks, making it an invaluable tool for businesses, individuals, and language professionals alike.
```

### 3.5. Retrieval Chain을 사용한 실습

"what is LangSmith?"라는 질문에 대한 답변을 정확하게 얻기 위해서는 LLM에게 추가적인 맥락을 제공해야 합니다. 이를 위해 **retrieval**을 사용할 수 있습니다. Retrieval을 사용하면 여러 데이터를 LLM에 직접 제공할 수 있습니다. 그런 다음 **retriever**를 사용하여 가장 관련성 높은 데이터를 가져올 수 있습니다.

우리는 Retriever를 통해 관련성이 높은 데이터를 가져와 Prompt에 전달하기 위해 몇 가지 component를 추가해야 합니다. 이 component에는 [**embedding model**](https://js.langchain.com/docs/modules/data_connection/text_embedding)와 [**vectorstore**](https://js.langchain.com/docs/modules/data_connection/vectorstores)가 포함됩니다.

먼저, 인덱싱할 데이터를 **로드**해야 합니다. 이를 위해 웹에서 데이터를 가져오기 위해 [**document loader**](https://js.langchain.com/docs/integrations/document_loaders/web/cheerio)와 데이터를 가져오기 위해 Cheerio 라이브러리를 사용할 것입니다.

```bash
npm install cheerio
```

다음과 같이 코드를 작성할 수 있습니다.

```jsx
import { CheerioWebBaseLoader } from "langchain/document_loaders/web/cheerio";

const loader = new CheerioWebBaseLoader(
  "https://docs.smith.langchain.com/user_guide"
);

const docs = await loader.load();

console.log(docs.length);
console.log(docs[0].pageContent.length);
```

```jsx
1;
36542;
```

로드된 문서의 크기가 매우 크므로 (크롤링된 데이터) 우리가 한 번에 처리할 수 있는 데이터 양 제한을 초과할 수 있습니다. 따라서, 문서를 관리 가능한 크기의 여러 청크로 **분할**해야 합니다. 이를 위해 [text splitter](https://js.langchain.com/docs/modules/data_connection/document_transformers/)를 사용할 수 있습니다.

```jsx
import { CheerioWebBaseLoader } from "langchain/document_loaders/web/cheerio";
import { RecursiveCharacterTextSplitter } from "langchain/text_splitter";

const splitter = new RecursiveCharacterTextSplitter();

const loader = new CheerioWebBaseLoader(
  "https://docs.smith.langchain.com/user_guide"
);

const docs = await loader.load();

const splitDocs = await splitter.splitDocuments(docs);

console.log(splitDocs.length);
console.log(splitDocs[0].pageContent.length);
```

```jsx
49;
441;
```

그 다음으로, 데이터를 vector store에 인덱싱해야 합니다. 이를 위해 [**embedding model**](https://js.langchain.com/docs/modules/data_connection/text_embedding) 및 [**vectorstore**](https://js.langchain.com/docs/modules/data_connection/vectorstores)가 필요합니다.

```jsx
import { OpenAIEmbeddings } from "@langchain/openai";
import { MemoryVectorStore } from "langchain/vectorstores/memory";

const embeddings = new OpenAIEmbeddings();
const vectorstore = await MemoryVectorStore.fromDocuments(
  splitDocs,
  embeddings
);
```

LangChain vectorstore 클래스는 embeddings 모델을 사용하여 자동으로 raw document (index)를 생성합니다.

vectorstore에 인덱싱된 데이터가 있으면 retrieval chain을 만듭니다. 이 chain은 사용자의 질문과 관련 문서를 vectorstore에서 찾은 다음 질문과 이러한 관련 문서를 LLM에 전달하여 질문에 답합니다.

먼저, 사용자 질문과 검색된 문서 (맥락 역할)을 입력으로 받아 답변을 생성하는 chain을 생성합니다.

```jsx
import { createStuffDocumentsChain } from "langchain/chains/combine_documents";
import { ChatPromptTemplate } from "@langchain/core/prompts";

const prompt =
  ChatPromptTemplate.fromTemplate(`Answer the following question based only on the provided context:

<context>
{context}
</context>

Question: {input}`);

const documentChain = await createStuffDocumentsChain({
  llm: chatModel,
  prompt,
});
```

이제 이 chain을 직접 실행하여 **질문**과 **문서** (맥락 역할)를 `documentChain.invoke`에 전달합니다.

```jsx
import { Document } from "@langchain/core/documents";

await documentChain.invoke({
  input: "what is LangSmith?",
  context: [
    new Document({
      pageContent:
        "LangSmith is a platform for building production-grade LLM applications.",
    }),
  ],
});
```

```jsx
LangSmith is a platform for building production-grade Large Language Model (LLM) applications.
```

그러나 우리는 Retriever를 통해 선택된 document를 chain에 제공하려고 합니다. Retriever는 chain에 모든 document를 제공하는 대신 chain에 제공할 가장 관련성 높은 document를 선택하기 때문입니다.

```jsx
import { createRetrievalChain } from "langchain/chains/retrieval";

const retriever = vectorstore.asRetriever();

const retrievalChain = await createRetrievalChain({
  combineDocsChain: documentChain,
  retriever,
});

const result = await retrievalChain.invoke({
  input: "what is LangSmith?",
});

console.log(result.answer);
```

```jsx
LangSmith is a tool developed by LangChain that is used for debugging and monitoring LLMs, chains, and agents in order to improve their performance and reliability for use in production.
```

전체 소스 코드

```jsx
import { CheerioWebBaseLoader } from "langchain/document_loaders/web/cheerio";
import { RecursiveCharacterTextSplitter } from "langchain/text_splitter";
import { OpenAIEmbeddings } from "@langchain/openai";
import { MemoryVectorStore } from "langchain/vectorstores/memory";
import { createStuffDocumentsChain } from "langchain/chains/combine_documents";
import { ChatPromptTemplate } from "@langchain/core/prompts";
import { createRetrievalChain } from "langchain/chains/retrieval";
import { ChatOpenAI } from "@langchain/openai";

const loader = new CheerioWebBaseLoader(
  "https://docs.smith.langchain.com/user_guide"
);
const splitter = new RecursiveCharacterTextSplitter();
const embeddings = new OpenAIEmbeddings();
const chatModel = new ChatOpenAI({});

const docs = await loader.load();
const splitDocs = await splitter.splitDocuments(docs);
const vectorstore = await MemoryVectorStore.fromDocuments(
  splitDocs,
  embeddings
);

const prompt =
  ChatPromptTemplate.fromTemplate(`Answer the following question based only on the provided context:

<context>
{context}
</context>

Question: {input}`);

const documentChain = await createStuffDocumentsChain({
  llm: chatModel,
  prompt,
});

const retriever = vectorstore.asRetriever();
const retrievalChain = await createRetrievalChain({
  combineDocsChain: documentChain,
  retriever,
});

const result = await retrievalChain.invoke({
  input: "what is LangSmith?",
});

console.log(result.answer);
```

### 3.6. 대화 검색 체인

위에서 한 작업은 우리가 한 가지 질문에만 답변할 수 있도록 도와주었습니다. 이 부분에서는 **여러 연속된 질문에 답변할 수 있는 체인**을 생성할 것입니다.

`createRetrievalChain` 함수를 사용할 때, 우리는 2가지를 변경해야 합니다:

1. 검색기는 가장 가까운 입력뿐만 아니라 대화 전반을 고려해야 합니다.
   - 우리의 검색기는 현재 **가장 가까운 입력과 관련된 문서만을 체인에 넣습니다.**
   - 대화 전반과 관련된 문서를 체인에 넣고 싶습니다.
   - 따라서 LLM은 새로운 검색 쿼리를 생성해야 합니다. 검색 쿼리는 대화 전반과 관련된 문서만을 필터링할 수 있는 새로운 검색기를 생성하는 데 도움이 될 것입니다.
2. 최종 LLM 체인도 대화 전반을 고려해야 합니다.
   - 우리의 LLM은 답변을 제공하기 위해 현재 컨텍스트와 **가장 가까운 입력만을 고려**합니다.
   - 우리는 LLM이 답변을 제공하기 위해 컨텍스트와 **대화 전반**을 고려할 수 있기를 원합니다.

```jsx
import { CheerioWebBaseLoader } from "langchain/document_loaders/web/cheerio";
import { RecursiveCharacterTextSplitter } from "langchain/text_splitter";
import { OpenAIEmbeddings } from "@langchain/openai";
import { MemoryVectorStore } from "langchain/vectorstores/memory";
import { createStuffDocumentsChain } from "langchain/chains/combine_documents";
import { ChatPromptTemplate } from "@langchain/core/prompts";
import { createRetrievalChain } from "langchain/chains/retrieval";
import { ChatOpenAI } from "@langchain/openai";
import { createHistoryAwareRetriever } from "langchain/chains/history_aware_retriever";
import { MessagesPlaceholder } from "@langchain/core/prompts";
import { HumanMessage, AIMessage } from "@langchain/core/messages";

const loader = new CheerioWebBaseLoader(
  "https://docs.smith.langchain.com/user_guide"
);
const splitter = new RecursiveCharacterTextSplitter();
const embeddings = new OpenAIEmbeddings();
const chatModel = new ChatOpenAI({});

const docs = await loader.load();
const splitDocs = await splitter.splitDocuments(docs);
const vectorstore = await MemoryVectorStore.fromDocuments(
  splitDocs,
  embeddings
);
const retriever = vectorstore.asRetriever();

const historyAwarePrompt = ChatPromptTemplate.fromMessages([
  new MessagesPlaceholder("chat_history"),
  ["user", "{input}"],
  [
    "user",
    "Given the above conversation, generate a search query to look up in order to get information relevant to the conversation",
  ],
]);

const historyAwareRetrieverChain = await createHistoryAwareRetriever({
  llm: chatModel,
  retriever,
  rephrasePrompt: historyAwarePrompt,
});

const historyAwareRetrievalPrompt = ChatPromptTemplate.fromMessages([
  [
    "system",
    "Answer the user's questions based on the below context:\n\n{context}",
  ],
  new MessagesPlaceholder("chat_history"),
  ["user", "{input}"],
]);

const historyAwareCombineDocsChain = await createStuffDocumentsChain({
  llm: chatModel,
  prompt: historyAwareRetrievalPrompt,
});

const conversationalRetrievalChain = await createRetrievalChain({
  retriever: historyAwareRetrieverChain,
  combineDocsChain: historyAwareCombineDocsChain,
});

const result2 = await conversationalRetrievalChain.invoke({
  chat_history: [
    new HumanMessage("Can LangSmith help test my LLM applications?"),
    new AIMessage("Yes!"),
  ],
  input: "tell me how",
});

console.log(result2.answer);
```

### 3.7. PDF loader chain

```jsx
import { PDFLoader } from "langchain/document_loaders/fs/pdf";
import { RecursiveCharacterTextSplitter } from "langchain/text_splitter";
import { MemoryVectorStore } from "langchain/vectorstores/memory";
import { OpenAIEmbeddings } from "@langchain/openai";
import { ChatPromptTemplate } from "@langchain/core/prompts";
import { ChatOpenAI } from "@langchain/openai";
import { createStuffDocumentsChain } from "langchain/chains/combine_documents";
import { createRetrievalChain } from "langchain/chains/retrieval";
import { StructuredOutputParser } from "langchain/output_parsers";

const splitter = new RecursiveCharacterTextSplitter();
const loader = new PDFLoader("public/pdf/invoice.pdf");
const embeddings = new OpenAIEmbeddings();
const chatModel = new ChatOpenAI({});

const docs = await loader.load();
const splitDocs = await splitter.splitDocuments(docs);
const vectorstore = await MemoryVectorStore.fromDocuments(
  splitDocs,
  embeddings
);

const parser = StructuredOutputParser.fromNamesAndDescriptions({
  total: "total amount of money in the invoice",
  tax: "total tax amount in the invoice",
});

const prompt =
  ChatPromptTemplate.fromTemplate(`Answer the following question based only on the provided context:

<context>
{context}
</context>

Generate the response based on this structured output:
{format_instructions}

Question: {input}`);

const documentChain = await createStuffDocumentsChain({
  llm: chatModel,
  prompt,
  outputParser: parser,
});

const retriever = vectorstore.asRetriever();
const retrievalChain = await createRetrievalChain({
  combineDocsChain: documentChain,
  retriever,
});

const result = await retrievalChain.invoke({
  input: "what is the total money of this invoice?",
  format_instructions: parser.getFormatInstructions(),
});

console.log(result.answer);
```
