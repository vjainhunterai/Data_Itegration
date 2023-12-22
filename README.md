Started by user GPO Administrator
Obtained JenkinsFile from git https://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@github.com/GPOAdmin/Integration.git
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in C:\Users\servicesaccount\AppData\Local\Jenkins\.jenkins\workspace\Prod_DataPipeline_Dec23_V1.0
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
The recommended git tool is: git.exe
No credentials specified
 > git.exe rev-parse --resolve-git-dir C:\ETL\Prod\Git_Code\.git # timeout=10
Fetching changes from the remote Git repository
 > git.exe config remote.origin.url https://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@github.com/GPOAdmin/Integration.git # timeout=10
ERROR: Error fetching remote repo 'origin'
hudson.plugins.git.GitException: Failed to fetch from https://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@github.com/GPOAdmin/Integration.git
	at hudson.plugins.git.GitSCM.fetchFrom(GitSCM.java:1003)
	at hudson.plugins.git.GitSCM.retrieveChanges(GitSCM.java:1244)
	at hudson.plugins.git.GitSCM.checkout(GitSCM.java:1308)
	at org.jenkinsci.plugins.workflow.steps.scm.SCMStep.checkout(SCMStep.java:129)
	at org.jenkinsci.plugins.workflow.steps.scm.SCMStep$StepExecutionImpl.run(SCMStep.java:97)
	at org.jenkinsci.plugins.workflow.steps.scm.SCMStep$StepExecutionImpl.run(SCMStep.java:84)
	at org.jenkinsci.plugins.workflow.steps.SynchronousNonBlockingStepExecution.lambda$start$0(SynchronousNonBlockingStepExecution.java:47)
	at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:539)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1136)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:635)
	at java.base/java.lang.Thread.run(Thread.java:833)
Caused by: hudson.plugins.git.GitException: Command "git.exe config remote.origin.url https://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@github.com/GPOAdmin/Integration.git" returned status code 128:
stdout: 
stderr: fatal: not in a git directory

	at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.launchCommandIn(CliGitAPIImpl.java:2671)
	at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.launchCommandIn(CliGitAPIImpl.java:2601)
	at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.launchCommandIn(CliGitAPIImpl.java:2597)
	at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.launchCommand(CliGitAPIImpl.java:1968)
	at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.launchCommand(CliGitAPIImpl.java:1980)
	at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.setRemoteUrl(CliGitAPIImpl.java:1594)
	at hudson.plugins.git.GitAPI.setRemoteUrl(GitAPI.java:161)
	at hudson.plugins.git.GitSCM.fetchFrom(GitSCM.java:991)
	... 11 more
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
ERROR: Error fetching remote repo 'origin'
Finished: FAILURE
