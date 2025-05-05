# Basics

Workflows v/s Jobs v/s Steps

|              |                                    Workflows                                     |                                Jobs                                |            Steps            |
| ------------ | :------------------------------------------------------------------------------: | :----------------------------------------------------------------: | :-------------------------: |
| Running flow | Triggered by events. Use concurrency groups to prevent parallel workflow trigger |                       Parallel or Sequential                       |         Sequential          |
| New Runner   |                                                                                  | Every job uses separate runner completely isolated from each other | Get executed on same runner |

> [!NOTE] Failed parent job and dependent job
> By default: Dependent jobs don't get executed if their `needs` job get failed

#### Metadata (context)

Can be accessed via `run: ${{ github }}`
Make more readable (parse to JSON) `run: ${{ toJSON(github) }}`
### Workflow file template

> [!NOTE]- Required keys
> - name (name of workflow)
> - on ([trigger_events](https://docs.github.com/en/enterprise-cloud@latest/actions/using-workflows/events-that-trigger-workflows))
> - job
> 	- needs (single job or array of jobs which need to run before)
> 	- <job_name>
> 	- runs-on (runner type)
> 	- steps
> 		- name (step name)
> 		- run (command to run) | uses (use action from marketplace)

> [!question]- How to run Multiple Bash cmds in single run block? 
> using `|` `|-` (used to put multiline cmds in YAML)
>
```yaml
...
	run: |
	echo "First output"
	echo "Second output",
```

#### Trigger events

![](attachment/d654506642d45e13796630be1f2f6e44.png)

Reusable workflows
- Referencing workflows: a single folder can have multiple different `.yml` workflows that can be referenced
- Referencing custom actions: unlike workflows, a custom action is defined within it's own folder and referenced using that folder's path


# Hands-on

1. [actions-katas](https://github.com/eficode-academy/github-actions-katas)
2. Udemy Course
	- [ ] basic workflow with manual trigger + parallel jobs + sequential steps
	- [ ] 

# Practice Ques.

- Where are workflows stored?
- Which lang are actions written in?
- 

# Queries

- How to use both `run` and `uses` in a single step?
	![](attachment/18f0bb236bb40801e44a194c09051643.png)
- how to communicate + exchange data between different jobs (since they are on different runners)
- is there are common storage to share between different runners?
- how to implement package & artifact caching to speed up jobs?
	- where is this cache stored and what's its life?
		- in case of GE Cloud v/s Self hosted runners?
	- we *SHOULD NOT* cache artifacts. WHY?
- how to use different runners within steps
- how to use same codeblocks across different jobs (avoid redundancy)? Is there a way to define a template with variables?
- for a job which depends on previous job => how to continue execution even though parent job failed?
- does steps/env override jobs/env variables that are of same name?
- how to use some env variables from repository scoped environments as well as some from manually created environments

> [!TODO] TODO
> - action vs workflow
> - rewatch publish download artifact video and what's the use of `id:`?
> - 


---

## **SCRIBBLE**

![](attachment/40d4444e7a684e525e70e06ea0b37370.png)

![](attachment/d11225fda4e331e6f52087a7200f17f9.png)

[Filter pattern cheat-sheet](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#filter-pattern-cheat-sheet)

![](attachment/ccd32a3a69c0f918b2a02c2818288364.png)

![](attachment/3b6c922ddd90cc6dc63aea6ab4451e1b.png)

![](attachment/6c974ec2335df4cb41c82766f056241d.png)

![](attachment/6011685f0bd47f2b3aca541ff2ead4d0.png)

![](attachment/93ed37d80a8231557498408233bb71db.png)

![](attachment/76ecb4258dd0e37d628b7440d377648e.png)

![](attachment/cff78a99fa45b915376dcbeb72512553.png)

[default environment variables](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)

![](attachment/9ea6e7a77ee95b431a04818d704c61cf.png)

different ways to access env variables:
![](attachment/6a70df5d060ae6c8764a23313e320819.png)

the secrets get hidden similar to Jenkins output
![](attachment/3b0be30df8d2f616c925b77d81a579a5.png)

![](attachment/0b8f692828d8acdf9eef7c69633d0049.png)

![](attachment/d33a83a9a18347681b5b97d6ee7f19aa.png)

![](attachment/1bbe650e88f3589116aeed14175dc1a0.png)

![](attachment/a882c82f8a2f3804d5814840ec030653.png)

re-usable workflows
![](attachment/7fc552bb58df404652706fdf27c5ebb7.png)
![](attachment/a7915aac9278e8c686804a5c3050a296.png)

![](attachment/cf92e9317090715cc21540cdf012769b.png)
![](attachment/01b952b040607d14706c3b034c79f44e.png)
![](attachment/251332a26e6d9fad55af19e84d5f32ae.png)

docker:
![](attachment/09d8087de6bb02356c5a758571aa99c8.png)
![](attachment/f9956bdd4d6d04d8037030443eabe949.png)

Communicating with service containers when job running on container
![](attachment/8c68d2805bedd37941617368eac45f20.png)
Communicating with service containers when job running on runner
![](attachment/b311dfb56788bc994d2e9250300e0bac.png)

![](attachment/ec6843692ae79e85658ec41fdf0e8823.png)

Accessing outputs of custom actions
![](attachment/9b36e9c5a32d90493df314aa9de63215.png)
![](attachment/e0c2d5ec9178f6f907038a71add6bbbb.png)



### stuff to do
- getting info + cancelling + skipping a workflowo using REST API
	- [skip workflow runs](https://docs.github.com/en/actions/managing-workflow-runs/skipping-workflow-runs)
- read up docker integration on docker in detail
- learn about security hardening for github runners + actions
	- find tools which can scan for vulner.
	- 

### Outline

- runner types
- trigger events
	- activity types
		- pull_request
			- from collaborators
			- from forks
	- event filters
- artifacts
	- rest API
	- other actions
		- using upload/download-artifact
- outputs 
- variables + env variables + secrets
	- default github environment variables
		- their alternative context referencing
	- vars. | env. | secrets.
	- [other contexts](https://docs.github.com/en/actions/learn-github-actions/contexts#about-contexts)
	- different environment
		- protection rules
	- PR execution attach
- cancelling + skipping workflow runs
- controlling flow
	- ignore failing steps
		- continue-on-error
	- if
	- special functions
		- failure()
		- success()
		- always()
		- cancelled()
- matrix jobs
- reusable workflows
	- using other local .yml workflows
	- custom actions (defined at folder level)
		- js, docker, composite
- docker
	- containers for main job
	- service containers
*Template*
- name
- on
	- workflow_call
		- inputs
		- outputs
		- secrets
- env
- jobs
	- env
	- runs-on
	- strategy
		- matrix
			- include
			- exclude
	- container
	- concurrency
	- needs
	- permissions
	- if
- steps
	- env
	- uses
		- with
	- run
	- if
	- name
	- shell


## Documentation Reference

- Workflow Trigger Events - https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows
- Workflow Syntax (jobs. on. env permissions run-name) - https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions
	- Expressions - https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/evaluate-expressions-in-workflows-and-actions
	- Context - https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/accessing-contextual-information-about-workflow-runs
	- Variables - https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/store-information-in-variables
- Pass information between Jobs/Steps - https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/passing-information-between-jobs
- Default Environment Variables already available - https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/store-information-in-variables#default-environment-variables
- Handle `step` and `job` errors - https://www.kenmuse.com/blog/how-to-handle-step-and-job-errors-in-github-actions

### Misc.

- Trigger workflows using API Calls - https://dev.to/teamhive/triggering-github-actions-using-repository-dispatches-39d1
- Control Github Actions with `gh` CLI
	- https://github.blog/news-insights/product-news/work-with-github-actions-in-your-terminal-with-github-cli/
	- https://www.youtube.com/watch?v=Nn8hYDhmizM