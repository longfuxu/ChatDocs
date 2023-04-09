# ChatDocs: Create a GPT Chatbot for Your PDF Files with the Power of LLM

Use the new GPT-4/gpt-3.5 api to build a chatGPT chatbot for multiple Large PDF files.

Tech stack used includes LangChain, Pinecone, Typescript, Openai, and Next.js. LangChain is a framework that makes it easier to build scalable AI/LLM apps and chatbots. Pinecone is a vectorstore for storing embeddings and your PDF in text to later retrieve similar docs.

## Development

1. Clone the repo

```
git clone [github https url]
```

2. Install packages

```
pnpm install 
```
You probably need to install pnpm and npm first

3. Set up your `.env` file

- Copy `.env.example` into `.env`
  Your `.env` file should look like this:

```
OPENAI_API_KEY=

PINECONE_API_KEY=
PINECONE_ENVIRONMENT=

PINECONE_INDEX_NAME=

```

- Visit [openai](https://help.openai.com/en/articles/4936850-where-do-i-find-my-secret-api-key) to retrieve API keys and insert into your `.env` file.
- Visit [pinecone](https://pinecone.io/) to create and retrieve your API keys, and also retrieve your environment and index name from the dashboard.
- When create your index in pinecone, make sure to set the Dimenstion as 1536, as this is the dimention that OpenAI embedding model will use
- You need to wait until the Index initialize -> ready, you can ingest your date, otherwise you will get error.[https://github.com/mayooear/gpt4-pdf-chatbot-langchain/issues/97]

4. In `utils/makechain.ts` chain change the `QA_PROMPT` for your own usecase. Change `modelName` in `new OpenAIChat` to `gpt-3.5-turbo` or `gpt-4`

## Convert your PDF files to embeddings

**This repo can load multiple PDF files**

1. Create a `docs`folder if it's not there. Inside `docs` folder, add your pdf files or folders that contain pdf files.
In this step, make sure you put files from different topic into deifferent subfolder, and put the embeddings into those corresponding subfolders, and also retrieval specific infomation from those folders
- Put your docs from different field into different subfolders, such as sci, low,literature
- Inside the `scipts` folder, edit the `ingest-data.ts` file, make sure in line 10 change to your specific file`const filePath = 'docs/sci'`, to avoid potential overwrite previous documents
- In the `config` folder, replace the `PINECONE_NAME_SPACE` with a `namespace` where you'd like to store your embeddings on Pinecone when you run `pnpm run ingest`. This namespace will later be used for queries and retrieval. *this is quite important if you only want to retrival a specific domain of knowledge*

2. Run the script `npm run ingest` to 'ingest' and embed your docs. If you run into errors troubleshoot below.

3. Check Pinecone dashboard to verify your namespace and vectors have been added.

## Run the app

Once you've verified that the embeddings and content have been successfully added to your Pinecone, you can run the app `pnpm run dev` to launch the local dev environment, and then type a question in the chat interface.

## Troubleshooting

In general, keep an eye out in the `issues` and `discussions` section of this repo for solutions.

**General errors**

- Make sure you're running the latest Node version. Run `node -v`
- Try a different PDF or convert your PDF to text first. It's possible your PDF is corrupted, scanned, or requires OCR to convert to text.
- `Console.log` the `env` variables and make sure they are exposed.
- Make sure you're using the same versions of LangChain and Pinecone as this repo.
- Check that you've created an `.env` file that contains your valid (and working) API keys, environment and index name.
- If you change `modelName` in `OpenAIChat` note that the correct name of the alternative model is `gpt-3.5-turbo`
- Make sure you have access to `gpt-4` if you decide to use. Test your openAI keys outside the repo and make sure it works and that you have enough API credits.
- Check that you don't have multiple OPENAPI keys in your global environment. If you do, the local `env` file from the project will be overwritten by systems `env` variable.
- Try to hard code your API keys into the `process.env` variables.


**Pinecone errors**

- Make sure your pinecone dashboard `environment` and `index` matches the one in the `pinecone.ts` and `.env` files.
- Check that you've set the vector dimensions to `1536`.
- Make sure your pinecone namespace is in lowercase.
- Pinecone indexes of users on the Starter(free) plan are deleted after 7 days of inactivity. To prevent this, send an API request to Pinecone to reset the counter before 7 days.
- Retry from scratch with a new Pinecone project, index, and cloned repo.

## Credit

This repo is a fork from [gpt4-pdf-chatbot-langchain
](https://github.com/mayooear/gpt4-pdf-chatbot-langchain/tree/feat/add-directory-loader), with modified personal prefernece

Frontend of this repo is inspired by [langchain-chat-nextjs](https://github.com/zahidkhawaja/langchain-chat-nextjs)
