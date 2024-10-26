# Integuru

An experimental agent that generated integration code by reverse engineering platform's internal APIs.

## What Integration Agent Does

You provide a file containing all browser network requests, a file with the cookies and a prompt describing the action triggered in the browser, and the agent outputs runnable python code that hits the platform's internal endpoints to perform the action you want.

## How It Works

Let's assume we want to download utility bills:

1. The agent identifies the request that downloads the utility bills.
   For example, the request URL might look like this:
   ```
   https://www.example.com/utility-bills?accountId=123&userId=456
   ```
   
2. It identifies parts of the request that depend on other requests.
   The above URL contains dynamic parts (accountId and userId) that need to be obtained from other requests.
   ```
   accountId=123 userId=456
   ```
3. It finds the requests that provide these parts and makes the download request dependent on them.
   ```
   GET https://www.example.com/get_account_id
   GET https://www.example.com/get_user_id
   ```
4. This process is repeated until the request depends on no other request and only requires the authentication cookies.
5. The agent traverses upwards throught the graph starting from nodes with no outgoing edges until it reaches the master node while converting each node to a runnable function.

## Features

- Generate a dependency graph of requests to make the final request that performs the desired action.
- Allow input variables (for example, choosing the YEAR to download a document from). This is only supported for the graph generation. Code generation for input variables coming soon!
- Generate code to hit all requests in the graph to perform the final action.

## Setup

1. Set up your OpenAI [API Keys](https://platform.openai.com/account/api-keys) and add the `OPENAI_API_KEY` environment variable. (We recommend you to have OpenAI API o1-mini/preview access)
2. Install Python requirements via poetry:
   ```
   poetry install
   ```
3. Open a poetry shell:
   ```
   poetry shell
   ```
4. Run the following command to spawn a browser:
   ```
   poetry run python create_har.py
   ```
   Login to your platform and perform the action you want (such as downloading a utility bill).
5. Run the Integration Agent:
   ```
   poetry run python -m integration_agent --prompt "download utility bills"
   ```
    You can also run it via jupyter notebook `main.ipynb`
## Usage

After setting up the project, you can use the Integration Agent to analyze and reverse engineer API requests for various platforms. Simply provide the appropriate .har file and a prompt describing the action you want to analyze.


```
poetry run python -m integration_agent --help
Usage: python -m integration_agent [OPTIONS]

Options:
  --model TEXT                    The LLM model to use
  --prompt TEXT                   The prompt for the model  [required]
  --har-path TEXT                 The HAR file path
  --cookie-path TEXT              The cookie file path
  --max_steps INTEGER             The optional max_steps (default is 10)
  --input_variables <TEXT TEXT>...
                                  Input variables in the format key value
  --generate-code                 Whether to generate the full integration
                                  code
  --help                          Show this message and exit.
```

## Contributing

Contributions to improve the Integration Agent are welcome. Please feel free to submit issues or pull requests on the project's repository.

## Info

Integration Agent is built by integuru.ai. Besides our work on the agent, we offer services to build custom integrations upon request, host, and manage authentication. If you have requests or want to work with us, reach out at richard@taiki.online.

We open source our integrations. You can find them here https://github.com/Integuru-AI/Integrations

