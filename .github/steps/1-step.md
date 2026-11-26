## Step 1: Introduction to AI Actions

In this exercise, you'll learn to integrate AI capabilities directly into your GitHub Actions workflows using GitHub Copilot. Let's start with understanding the key concepts and then straight to creating your first AI-powered workflow!

### 📖 Theory: GitHub Copilot in Actions

#### 🤖 What is GitHub Copilot?

**[GitHub Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli)** provides access to AI models from a command-line interface. The GitHub Models inference API used by earlier versions of this exercise was [retired](https://github.blog/changelog/2026-07-30-github-models-is-now-retired/).

#### ⚙️ How GitHub Actions work with GitHub Copilot

The [integration](https://docs.github.com/en/copilot/how-tos/copilot-cli/automate-copilot-cli/automate-with-actions) between GitHub Actions and GitHub Copilot uses the Copilot CLI:

- 🔑 **Authentication**: Create a fine-grained personal access token with the **Copilot Requests** permission for an account with Copilot access, and save it as an Actions repository secret named `COPILOT_PAT`.

- 🔐 **Minimal Permissions**: The workflow does not need repository permissions on its built-in `GITHUB_TOKEN`.

- 🎯 **Easy Integration**: The official [actions/ai-inference](https://github.com/actions/ai-inference) action invokes Copilot CLI to generate a response.

> [!TIP]
>
> Want to dive deeper? Check out these resources:
>
> - 📖 [Automating Copilot CLI with GitHub Actions](https://docs.github.com/en/copilot/how-tos/copilot-cli/automate-copilot-cli/automate-with-actions)

### ⌨️ Activity: Create Your First AI Workflow

Now that you understand the concepts, let's put them into practice! Open a new tab of this repository to follow these steps.

Let's create a simple workflow that we can trigger manually from the GitHub UI.

1. Navigate to the `Code` tab of your repository. Then into `.github/workflows/` directory.

1. Click `Add File` and create a new workflow file named

   ```text
   ask-ai.yml
   ```

1. Start by adding the workflow name, manual event trigger and required permissions:

   ```yaml
   name: Ask AI
   on:
     workflow_dispatch:

   permissions: {}
   ```

   > ❗ **Caution:** Copy the contents as provided, as this exact workflow name (`Ask AI`) is required to progress to next steps of this exercise.

1. Now we'll add a job that uses the AI inference action.

   In this simple scenario, we'll ask the AI a simple hardcoded question and display the response in the workflow summary:

   ```yaml
   jobs:
     ask-ai:
       runs-on: ubuntu-latest

       steps:
         - name: Install Copilot CLI
           run: npm install -g @github/copilot@1.0.116

         - name: AI Inference
           id: ai-response
           uses: actions/ai-inference@v3
           env:
             COPILOT_GITHUB_TOKEN: {% raw %}${{ secrets.COPILOT_PAT }}{% endraw %}
           with:
             model: auto
             prompt: |
               Give me a programming joke.

         - name: Display AI Response
           env:
             RESPONSE_FILE: {% raw %}${{ steps.ai-response.outputs.response-file }}{% endraw %}
           run: |
             echo "## 🤖 AI Response" >> "$GITHUB_STEP_SUMMARY"
             echo "" >> "$GITHUB_STEP_SUMMARY"
             cat "$RESPONSE_FILE" >> "$GITHUB_STEP_SUMMARY"
   ```

   > ❗ **Caution:** Be mindful of YAML formatting! GitHub's file editor will show red underlines for certain YAML errors.

1. Commit the workflow file directly to the `main` branch.

### ⌨️ Activity: Test Your AI Workflow

Now let's test the workflow you just created to see AI in action!

1. Navigate to the **Actions** tab in your repository.

1. In the left sidebar, look for the **Ask AI** workflow in the workflow list and click on it.

1. Click the **Run workflow** button, keep the default branch selected, and click the green **Run workflow** button to trigger it.

   <img width="900" alt="run workflow manual trigger" src="https://github.com/user-attachments/assets/89d96ce7-ca5e-4f5f-b8d0-25ebd5cdc4d6" />

1. Wait for the workflow to complete and check the workflow run summary to see the AI's response displayed in a nicely formatted way.

1. As your workflow completes successfully, Mona will automatically prepare the next step in your learning journey!

<details>
<summary>Having trouble? 🤷</summary><br/>

- **Workflow fails to run**: Ensure the workflow is complete and properly yaml formatted, if it's not then:
  - Find the issue in the workflow and commit the changes again to `main` branch
  - Try running the workflow again
- **No AI response**: Make sure the `id: ai-response` is set on the AI Inference step and referenced correctly in the Display step
- **Authentication errors**: Verify `COPILOT_PAT` is configured as an Actions secret, has the **Copilot Requests** permission, and belongs to an account with Copilot access
- **Action not found**: Verify you're using the exact action name: `actions/ai-inference@v3`

</details>
