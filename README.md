---
uid: mb_ext_prassistant
---
# Pull Request AI Assistant (PRAssistant)

<div style="margin-bottom: 20px; padding: 10px 20px 20px 20px; border: 2px solid #FF9800; border-radius: 4px; background-color: #FFF3E0; box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);">
  <strong style="display: block; margin-bottom: 10px; margin-top: 10px; color: #E65100;">Important Consideration:</strong>
  <div style="padding: 20px; border: 1px solid #FF9800; border-radius: 4px; background-color: #FFE0B2;">
    <p style="margin: 0;">Just like other AI tools powered by LLMs, PR Assistant can be incorrect. You should verify the PRAssistant’s suggestions before accepting them. You can think of the PR assistant as an enhancement, not a replacement, and you should continue to follow existing code review best practices.</p>
  </div>
</div>

<div style="padding: 20px; border: 2px solid #4CAF50; border-radius: 4px; background-color: #e8f5e9; box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);">
  <h2 style="color: #388E3C; text-align: center; margin-top: 0;">Announcement</h2>
  <blockquote style="font-style: italic; margin: 0;">
    <p>So far more than 10K repos have self onboarded to PRAssistant. Starting in FY25 Q1 (July 2024), 
    1ES will start rolling out to different Microsoft organizations on Azure DevOps by default. 
    We are having communications with E+D, C+AI and Microsoft security engineering systems teams to drive this effort.</p>
    <p>This decision was primarily driven by a suggestion from Scott Gu, based on demonstrated efficiency gains in pull request completion time, reported improvements in code quality, and several requests around discoverability and enabling by default.</p>
    <p>Learn more below or visit specific docs to see configuration options or how to opt out.</p>
  </blockquote>
</div>

---

**Introduction:** The PR assistant is a pull request AI copilot powered by large language models (LLM). It supplements the current pull request experience for PR authors and reviewers by generating code change summaries for pull request descriptions and by performing code reviews.


**What to expect:** 
- Teams use the PR assistant as an AI reviewer before human reviews. This allows a PR author to catch and fix some things early, saving human reviewers time and effort. 

- We have also seen teams use the PR assistant comments as a learning tool, using the suggestions as a discussion point for the pros and cons of various approaches. 

If you are not onboarded to auto expereince yet, you can do so following [onboarding steps](https://eng.ms/docs/products/gitops/apps/extensions/prassistant#onboarding) . 

Experience works on first iteration of PR. For forthcoming iterations, you can manually invoke the experience using [copilot commands](PRAssistantCopilot.md)

# PRAssistant Bot Introduction
PRAssistant Bot is based on generative-AI and is designed for below features
1. Automated code review 
2. Automated description update
3. User-initiated commands on any PR [copilot commands](PRAssistantCopilot.md)
5. Customize standard code review experience with [options](SampleFiles/PRAssistant_CustomizeCodeReview.md)
6. Develop new AI on PR scenario via prompt-as-config [instructions](SampleFiles/PRAssistant_promptasconfig.md)

Note:
- Currently review or description update takes place only at PR create and not at PR update.
- It can take a few minutes for these actions to take place since timing primarily depends on interaction with AI. 
- It is possible sometimes the action does not take place at all due to connectivity issue or no response generated.

## Disclaimer
1. AI-generated suggestions can be inaccurate and can even contain security flaws. Please verify the suggestions before taking any associated actions.
2. You should continue to follow the existing code review best practices. 

## Onboarding

### Auto code review and Auto description update
**You should be a repo owner or have a confirmation from repo owner before onboarding your repo.** Also please inform your team that they have been onboarded to PRAssistant.

[Create a fork and a PR](https://github.com/gitops-microsoft/GitOps.Defaults/blob/main/app-primitives/pull-request-copilot-activation.yml) in the GitOps.Default repository. 
You may not have permissions to merge, but as long as a PR is created successfully, it should get auto-merged if there are no issues. Alias for github login will be MicrosoftAlias_Microsoft.  

#### Note: It can take 12 - 24 hours for approval and deployment of your PR

If you are tented project, then please review [data logging](https://eng.ms/docs/products/gitops/apps/extensions/Private-tented-dataflow) before onboarding. 

### User-initiated [copilot commands](PRAssistantCopilot.md) on any PR
We have enabled the user-initiated experience by default to majority of organization across Microsoft. If the commands are not working, please reach out to merlinbotdri@microsoft.com

### How to provide feedback
Your feedback is very important to continuously enhance user experience thereby please provide your feedback by selecting useful/ not-useful feedback option and by filling an optional form.

## Want to update default settings?


### THIS SECTION IS ONLY IF YOU WANT TO UPDATE THE DEFAULT EXPERIENCE


This is the default experience provided to you [Default Config file](SampleFiles/PRAssistantRepoYaml.md) - you do not have to add this to your repo unless you want to update any configuration. Only for msasg, you will have to consider [msasg default config](SampleFiles/PrAssistantRepoYaml-WebXT.md) file. 

If you want to update the default experience then add the above yaml file to your repo at .azuredevops/policies/ location and update the settings you want to change (shared below). Make sure the file is merged in the default branch for settings to be effective.

### Default configurations that can be updated:

Include/ exclude
- Opt-in for only auto code review or auto description update
- Exclude an alias from auto experience
- Include or exclude certain files
- Files with particular content should not be reviewed

Code review experience 
- Customize code review expereince
-   Additional Instructions
-   Focus areas
-   Criticality
- Develop a new code review prompt (via Prompt as config) 
- Code review comments should be in closed state
- Update the max number of code review comments - default is 3
  
Description experience 
- Add AI summary on top of the PR description field
- AI description should be auto posted as a comment instead of updating the description directly

#### Want automated description update but want automated code review 
Add the above default config file your repo and just remove the below lines from default
```yaml
- prompt: 'copilot: summary'    # Append pull request summary with an AI-Generated description.
  autoResolve: true             # Auto resolve a comment posted after pull request summary is generated.
```
#### Want automated code review but want automated description update
Add the above default config file your repo and remove the below lines from default
```yaml
- prompt: 'copilot: review'     # Perform code review on create of a pull request. 
  autoResolve: false            # Auto resolve code review feedback comment.
  detailed: false               # Should the generated review feedback be detailed.
```
#### Auto experiences to work only on certain target branches
Then add the above default config file to your repo and add a section for branches
```yaml
enabled: true                   # Enable pull request assistant.
branches:                       # Extension runs only on pull requests when target branch is in list of branches, by default runs on all branches
- master                        # Run extension only when target branch is either main or master
- main 
```
#### Want auto experience to include or exclude certain filepath
Add the above default config file your repo.
These gitignore style glob patterns can be used when generating responses. You have the option to `exclude` or `include` certain files.

The `exclude` section instructs the extension to **never** run on certain files: 
```yaml
exclude: 
  pathFilters: 
  # The exclude property should be used to restrict pull request assistant from sending any files to AI that may contain sensitive or proprietary information.
  - '.vscode/**'                # Exclude all files in directory .vscode and its sub directories
  - '*.[Ee][Xx][Ee]'            # Exclude all files with extension .exe (case insensitive)
```

The `include` pattern instructs the extension to **only** run on certain files:
```yaml
include: 
  pathFilters: 
  - 'src/**'                     # Include all files in directory src and its sub directories
  - '*.bicep'                    # Include all files with extension .bicep
```

>Note: by default, all files are included if there is no specific include/exclude configuration

#### Top-Level Exclude/Include vs Per-Action Configuration Exclude/Include
Add the above default config file your repo.
You may customize `exclude` and `include` at both the 'Top-Level' and 'Per-Action' sections of your configuration for fine-tuned behavior.

For example:
```yaml
enabled: true                   
branches:                       
- master                        
- main 
include:
  pathFilters:
  - 'src/**'                     # In ALL requests to the extension, only consider files under the src/ directory
exclude:
  pathFilters:
  - '*.[Ee][Xx][Ee]'             # In ALL requests to the extension, never consider files matching '*.exe' (case insensitive)
onPullRequestCreate:            
- prompt: 'copilot: review'      
  autoResolve: false            
  detailed: false               
  exclude:                      
    pathFilters:
    - '*.json'                   # When performing automatic 'review' on PullRequestCreate, exclude all files matching '*.json'
- prompt: 'copilot: summary'    
  autoResolve: true
  include:
    pathFilters:
    - '*.cs'                     # When performing automatic 'summary' on PullRequestCreate, only process files that match '*.cs'              
```

#### Exclude a user from auto experiences 
Add the above default config file your repo and add below configuration for excluding user
```yaml
- prompt: 'copilot: review'     # Perform code review on create of a pull request. 
  ignoreUser:             
  - UserAlias1                  #Alias of users to be excluded from auto experience 
  - UserAlias2 

- prompt: 'copilot: summary'     # Perform code review on create of a pull request. 
  ignoreUser:             
  - UserAlias1                  #Alias of users to be excluded from auto experience 
  - UserAlias2                   
```
#### Do not want files with particular content to be sent for review
Add the above default config file your repo and add Content filter below prompt: 'copilot: review'
```yaml
- prompt: 'copilot: review'     # Perform code review on create of a pull request. 
  exclude:             
    contentFilters: 
    - '(i?)auto-generated file'       #If any file has this content, the file will not be sent to AI for review
```

#### Update max code review comments in a PR for code review
Add the above default config file your repo and update the below configuration
```yaml
maxReviewCommentsAllowed: 3     #For Inline code review style, max comments that will be posted on a PR
```

#### Add AI summary on top of the PR description field  
Add the above 'default config' file your repo and add below configuration 
```yaml
- prompt: 'copilot: summary'     # Perform code review on create of a pull request. 
  prependAISummary: true                          
```

#### If you want automated description as a comment instead of updating the description
Add the above default config file your repo and update 'copilot: summary' prompt to 'copilot: description'
Example
```yaml
- prompt: 'copilot: description'    # Append pull request summary with an AI-Generated description.
  autoResolve: true             # Auto resolve a comment posted after pull request summary is generated.
```
### FAQ

#### 1. Want to get the code review comment in resolved state by default
Follow the steps [here](https://eng.ms/docs/products/gitops/apps/extensions/prassistant#want-to-update-default-settings) and set 'autoResolve: true' under for code review

#### 2. PRs are not getting code review?
You will not get an automated code review if 
  - AI did not find any issues to be addressed 
  - Your PR file is too long for AI to process (due to token limits)
  - You have include filter specified and none of the files in the pull request qualify
  - You file or path has been excluded. Or you have include filter and none of the  files in the pull request qualify. You can verify this by checking your repo '.azuredevops/policies/' for PRAssistant related yaml config
  - If AI service failed to respond
Please verify if other PRs in your repo are getting AI generated code review

#### 3. AI generated description is not getting added
You may not get an automated description update if 
  - You file or path has been excluded. Or you have include filter and none of the  files in the pull request qualify. You can verify this by checking your repo '.azuredevops/policies/' for PRAssistant related yaml config
  - If AI service failed to respond
  - If PR was closed or abandoned before AI could respond with updated summary.
Please verify if other PRs in your repo are getting AI generated description update 

#### 4. Want auto description update or code review on latest iteration of the PR
You can do so by enabling copilot experience in your repo (if not already enabled). 
  - Verify if your repo is onboarded to copilot (user triggered experience). Steps to enable copilot experience are [here](https://eng.ms/docs/products/gitops/apps/extensions/prassistant#how-to-onboard-to-the-copilot-user-triggered-expereinces).
  - You can type **copilot: review** to get code review or **copilot: summary** to update your summary with latest iteration of the PR.
  - Copilot capability (user triggered commands) [how to use](PRAssistantCopilot.md).

#### 4. Want auto description update or code review on latest iteration of the PR
You can do so by enabling copilot experience in your repo (if not already enabled). 
  - Verify if your repo is onboarded to copilot (user triggered experience). Steps to enable copilot experience are [here](https://eng.ms/docs/products/gitops/apps/extensions/prassistant#how-to-onboard-to-the-copilot-user-triggered-expereinces).
  - You can type **copilot: review** to get code review or **copilot: summary** to update your summary with latest iteration of the PR.
  - Copilot capability (user triggered commands) [how to use](PRAssistantCopilot.md).

#### 5. What all file types are supported for code reviews?
You can find the latest list [here](https://eng.ms/docs/products/gitops/apps/extensions/PrAssistant-languages).

#### 6. Want pull request summary placed appropriately in the pull request template instead of being appended to the summary?
Please add **AI_Description_Placeholder** to the pull request template where the summary needs to be placed. 


<div style="background-color: blue; color: white; padding: 10px; word-wrap: break-word;">
  
</div>
