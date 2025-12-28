# Style Check PR Workflow - AI Integration Guide

This workflow evaluates C# code style compliance using AI instead of regex patterns. It generates a comprehensive prompt that includes your style guidelines and changed files, then sends it to an AI service for evaluation.

## Current Status

⚠️ **AI integration is not yet configured** - The workflow currently uses a placeholder that always passes.

## How It Works

1. **Detects changed .cs files** in the PR
2. **Loads the prompt template** from `.github/prompts/evaluate-csharp-style.prompt.md`
3. **Injects changed files** into the prompt template
4. **Sends to AI service** for intelligent analysis (when configured)
5. **Posts results** as PR comment with categorized violations and summary statistics

## AI Service Integration Options

Choose ONE of the following options and configure it in `.github/workflows/style-check-pr.yml`:

### Option 1: GitHub Copilot CLI (Recommended)

**Prerequisites:**

- Install GitHub CLI: `gh extension install github/gh-copilot`
- GitHub Copilot subscription

**Configuration:**

```yaml
# In style-check-pr.yml, uncomment lines for Option 1:
$response = gh copilot suggest "$prompt" --json | ConvertFrom-Json
$evaluation = $response | ConvertFrom-Json
```

**Pros:**

- Native GitHub integration
- No additional API keys needed
- Works with your existing Copilot subscription

**Cons:**

- Requires gh CLI extension
- May have usage limits

### Option 2: OpenAI API

**Prerequisites:**

- OpenAI API key
- Active OpenAI account with credits

**Configuration:**

1. Add secret to repository:

   - Go to: Repository → Settings → Secrets and variables → Actions
   - Add new secret: `OPENAI_API_KEY`
   - Value: Your OpenAI API key

2. Uncomment Option 2 code in `style-check-pr.yml`

**Environment Variables:**

- `OPENAI_API_KEY` (required)

**Pros:**

- Direct API access
- Reliable and fast
- Support for GPT-4 and other models

**Cons:**

- Costs money per API call
- Requires separate OpenAI account

### Option 3: Azure OpenAI

**Prerequisites:**

- Azure subscription
- Azure OpenAI resource deployed
- Deployment name configured

**Configuration:**

1. Add secrets to repository:

   - `AZURE_OPENAI_API_KEY` - Your Azure OpenAI API key
   - `AZURE_OPENAI_ENDPOINT` - Your endpoint (e.g., `https://your-resource.openai.azure.com`)
   - `AZURE_OPENAI_DEPLOYMENT` - Your deployment name (e.g., `gpt-4`)

2. Uncomment Option 3 code in `style-check-pr.yml`

**Pros:**

- Enterprise-grade security
- Better privacy controls
- Integration with Azure ecosystem

**Cons:**

- More complex setup
- Requires Azure subscription
- Additional cost

## Setup Instructions

### Step 1: Choose Your AI Service

Review the options above and choose the one that best fits your needs.

### Step 2: Configure Secrets (if needed)

For OpenAI or Azure OpenAI:

1. Go to your repository on GitHub
2. Navigate to: **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add the required secrets for your chosen service

### Step 3: Update the Workflow

1. Open `.github/workflows/style-check-pr.yml`
2. Find the "AI INTEGRATION OPTIONS" section
3. **Uncomment** the code block for your chosen service
4. **Delete or comment out** the placeholder section
5. Commit the changes

### Step 4: Test the Integration

1. Create a test branch with a C# style violation
2. Open a PR
3. Check the workflow run in the Actions tab
4. Verify the PR comment shows detected violations

## Prompt File Structure

The generated prompt includes:

````
# Task: Evaluate C# Code Style Compliance

## Style Guidelines
[Full content of style-instructions.md]
[Full content of csharp-style-instructions.md]

## Files to Evaluate
### File: calculator.tests/FunctionTests.cs
```csharp
[Full file content]
````

## Instructions

[Specific evaluation criteria and JSON output format]

````

## Prompt Template

The workflow uses a reusable prompt template:
📄 **`.github/prompts/evaluate-csharp-style.prompt.md`**

**Features:**
- Complete style guidelines with examples
- Clear JSON output format specification
- Context-aware evaluation rules
- Can be used manually with any AI service

The workflow automatically injects changed C# files into this template.

## Expected AI Response Format

The AI must return valid JSON with summary statistics:

```json
{
  "result": "pass",
  "summary": {
    "totalFiles": 1,
    "filesWithViolations": 0,
    "majorViolations": 0,
    "minorViolations": 0
  },
  "violations": []
}
````

Or with violations:

```json
{
  "result": "fail",
  "summary": {
    "totalFiles": 2,
    "filesWithViolations": 1,
    "majorViolations": 1,
    "minorViolations": 1
  },
  "violations": [
    {
      "file": "calculator.tests/FunctionTests.cs",
      "line": "N/A",
      "category": "Namespace Naming",
      "severity": "Minor",
      "issue": "Namespace uses lowercase 'calculator.tests' instead of PascalCase 'Calculator.Tests'",
      "example": "Current: namespace calculator.tests | Expected: namespace Calculator.Tests"
    }
  ]
}
```

## Troubleshooting

### Workflow fails with "AI evaluation not configured"

**Solution:** You haven't uncommented an AI integration option yet. Follow Step 3 above.

### API authentication errors

**Solution:**

- Verify secrets are correctly set in repository settings
- Check secret names match exactly
- Ensure API key is valid and not expired

### Prompt too large errors

**Solution:**

- Some AI services have token limits
- Consider using a more capable model (GPT-4 Turbo, Claude 3, etc.)
- Split evaluation into multiple calls for large PRs

### JSON parse errors

**Solution:**

- The AI response must be valid JSON only
- Check the system prompt instructs AI to return only JSON
- Adjust temperature to 0.1 for more deterministic output

## Cost Considerations

### GitHub Copilot

- Included with existing Copilot subscription
- No additional cost for reasonable usage

### OpenAI

- Costs vary by model:
  - GPT-4: ~$0.03-0.06 per PR review
  - GPT-3.5-Turbo: ~$0.002-0.005 per PR review
- Exact cost depends on prompt size (number/size of changed files)

### Azure OpenAI

- Similar to OpenAI pricing
- May have different pricing tiers based on Azure contract

## Customization

### Adjust Evaluation Criteria

Edit the prompt generation in `style-check-pr.yml` to:

- Add/remove specific checks
- Change severity levels
- Focus on specific violations

### Change AI Model

Modify the API call to use different models:

- OpenAI: Change `model` parameter (gpt-4, gpt-3.5-turbo, etc.)
- Azure: Use different deployment name
- GitHub Copilot: Uses default model

### Modify Output Format

Change the PR comment format by editing the markdown report generation in the workflow.

## Best Practices

1. **Start with GPT-3.5-Turbo** for cost-effectiveness, upgrade if needed
2. **Set temperature to 0.1** for consistent, deterministic results
3. **Review the prompt file** in artifacts to debug issues
4. **Test with small PRs first** before rolling out broadly
5. **Monitor costs** in your AI service dashboard

## Artifacts

Each workflow run saves these artifacts (30-day retention):

- `style-evaluation-prompt.txt` - The full prompt sent to AI
- `evaluation-result.json` - Raw AI response
- `style-report.md` - Formatted report (if violations found)

Download from: Actions → Workflow run → Artifacts

## Support

For issues:

1. Check workflow logs in Actions tab
2. Download artifacts to review prompt and response
3. Verify secrets are configured correctly
4. Test API connection outside workflow first

## Alternative: Manual Review

If you don't want to set up AI integration:

1. Download the `style-evaluation-prompt.txt` artifact
2. Copy it to GitHub Copilot, ChatGPT, or Claude
3. Get the evaluation result
4. Manually post as PR comment

This is useful for testing before automating.
