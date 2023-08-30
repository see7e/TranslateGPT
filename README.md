# Automatic Translation of Markdown Files using GitHub Actions

> [!WARNING]  
> This bot still must be tested, all of this information is just a canception of an idea that I've had. If you found this and you already done the same, PLEASE let me know, so I'll revise the information here.

This document explains the provided GitHub Actions workflow code that automates the translation of Markdown files using the OpenAI API. The workflow monitors for changes in Markdown files, translates them into the desired language, and stores the translations in a "translate" folder. Please follow the steps below to set up this workflow in your repository.

> [!WARNING]  
> To use the `OPENAI_API_KEY`, you need a valid OpenAI account with a paid subscription. The API key is used to authenticate and authorize your access to OpenAI's API services, including language translation.

## Workflow Explanation

1.  **Triggering the Workflow**: The workflow is triggered whenever changes are pushed to the repository, specifically when Markdown files are added or updated.
    
    ```yaml
    on:
      push:
        paths:
          - '**/*.md'
    ```
    
2.  **Job Setup**: The workflow consists of a single job named "translate" that runs on an Ubuntu environment.
    
    ```yaml
    jobs:
      translate:
        runs-on: ubuntu-latest
    ```
    
3.  **Checkout Code**: The code from the repository is checked out for the workflow to operate on.
    
    ```yaml
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
    ```
    
4.  **Set up Python and Dependencies**: Python is set up, and the required dependencies, including the `openai` library, are installed.
    
    ```yaml
      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: 3.x
    
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install openai
    ```
    
5.  **Create "translate" Folder**: The workflow creates a "translate" folder if it doesn't exist.
    
    ```yaml
      - name: Create translate folder
        run: mkdir -p translate
    ```
    
6.  **Translate and Save Files**: The workflow iterates through all Markdown files, translates them using the OpenAI API, and saves the translations in the "translate" folder.
    
    ```yaml
      - name: Translate and save files
        run: |
          for file in $(find . -name "*.md"); do
            filename=$(basename -- "$file")
            target_lang="es" # Replace with your desired target language code
            translated_filename="translate/${filename%.*}_${target_lang}.md"
            openai api translate -i "$file" -o "$translated_filename" --target-language "$target_lang"
          done
    ```
    
7.  **Commit and Push Translations**: The workflow configures a temporary user for committing and pushing the translated files back to the repository.
    
    ```yaml
      - name: Commit and push translations
        run: |
          git config --global user.name "GitHub Actions"
          git config --global user.email "actions@github.com"
          git add translate
          git commit -m "Add translated files"
          git push
    ```
    

## Possible Issues

-   **API Key**: Ensure that you have an OpenAI API key properly configured in your repository secrets or settings. Without a valid API key, the translation won't work.
    
-   **Target Language**: Make sure you set the correct target language code (e.g., `"es"` for Spanish) in the workflow. Using an unsupported language code might result in errors or incorrect translations.
    
-   **API Usage and Costs**: Be mindful of the OpenAI API usage and potential costs associated with translation, especially if processing a large number of files. Keep an eye on your usage to prevent unexpected charges.
    

## Setting up the Workflow

1.  Copy the provided workflow code into a `.github/workflows/translate.yml` file in your repository.
    
2.  Make sure you have the OpenAI Python library (`openai`) installed in your project. If not, you can install it using the `pip install openai` command.
    
3.  Replace `"es"` with the desired target language code in the workflow code.
    
4.  Configure your OpenAI API key in your repository's secrets or settings as `OPENAI_API_KEY`.
    
5.  Push the changes to your repository.
    

With these steps, the workflow will be set up to automatically translate Markdown files and save the translations in the "translate" folder whenever you push changes to your repository.

---

# Some question answers

## [Is ChatGPT A Good Translator?](https://github.com/wxjiao/Is-ChatGPT-A-Good-Translator#----is-chatgpt-a-good-translator----)

## How should the .yml file be named?

The `.yml` file that contains your GitHub Actions workflow should be named according to a specific convention in order to be recognized and executed by GitHub Actions. The convention is as follows:

```
<workflow-name>.yml
```

Here, `<workflow-name>` is the name you give to your workflow. For example, if you want to name your workflow "Translate Markdown Files," your `.yml` file should be named something like:

```
translate-markdown-files.yml
```

Make sure to follow this naming convention to ensure that GitHub Actions recognizes and executes your workflow properly. Additionally, place the `.yml` file inside a directory named `.github/workflows` at the root of your repository. This is where GitHub expects workflow files to be stored.

Your directory structure would look something like this:

```
your-repo/
├── .github/
│   └── workflows/
│       └── translate-markdown-files.yml
├── file1.md
├── file2.md
└── ...
```

This way, when you push changes to your repository, GitHub Actions will automatically detect and run the workflow specified in the `.yml` file within the `.github/workflows` directory.

## How you can obtain an API key:

1.  **Sign Up for OpenAI**: If you haven't already, sign up for an OpenAI account on the [OpenAI website](https://www.openai.com/).

2.  **Subscribe to a Plan**: Choose a subscription plan that includes access to the OpenAI API. Different plans may have varying levels of access, usage, and associated costs.

3.  **Access API Key**: Once you're subscribed, you'll be able to access your API key. OpenAI typically provides you with an API key that you can use to authenticate your API requests.

4.  **Keep Your API Key Secure**: API keys are sensitive information. Keep your API key secure and avoid sharing it publicly. In GitHub, you can store your API key as a secret in your repository settings. This prevents others from accessing your key directly from the repository.

Here's how you can set up your API key as a secret in your GitHub repository:

1.  Go to your GitHub repository.
2.  Click on "Settings."
3.  In the left sidebar, click on "Secrets."
4.  Click on "New repository secret."
5.  Name the secret `OPENAI_API_KEY` and paste your actual API key value.
6.  Click "Add secret" to save the secret.