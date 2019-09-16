Azure Boards is a tool in Azure DevOps to help teams plan the work that needs to be done. The Tailspin team will use this tool to get a better idea of what work needs to be done and how to prioritize it.

Mara created her own project on Azure Boards using the Basic process. It shows the problems in the build process that she and Andy identified. Mara gets the team together for a quick demo.

**Mara**: Hi everyone. I set up Azure Boards and wanted to show you some work items I came up with.

**Andy**: What's a work item?

**Mara**: Work items help us plan and manage a project. A work item can track all kinds of activities. Maybe it's a task to do, a bug to fix, or some other issue. We can assign them to people and keep track of their progress.

Perhaps it's easier to show you. Here's Azure Boards using the Basic process.

![Azure Boards showing the initial three tasks](../../shared/media/build-initial-tasks.png)

**Amita**: Tell us about the Basic process. Are there other options?

**Mara**: There are four processes to choose from. We can use:

* **Capability Maturity Model Integration (CMMI)**. This is really for large organizations and it's pretty complicated so I didn't use it.
* **Scrum**. Scrum depends on a Scrum master who leads the Scrum team. The Scrum master makes sure everybody understands Scrum theory, practices, and rules. We don't have a Scrum master; that's someone who's usually received some training and certification so I didn't pick that one either.
* **Agile**. This seemed like the obvious choice since I'm always talking about Agile, but it has a few more things to consider than the simplest option.
* **Basic**. Basic is, well, basic. It's simple but gives us enough power to start doing effective planning right away, and that's why I picked it. In the Basic workflow, you move work from **To Do** to **Doing** to **Done**.

**Amita**: OK, let's use it to get started. We can switch to something else, right?

**Mara**: Right! So, let's pick a few work items we think we can fix in a couple weeks.

Andy can identify with these issues, but the rest of the team has questions.

**Tim**: These are mostly dev problems. But while we're on the subject, other teams are talking about code vulnerabilities, and I've been asked to show that our code is secure. Is there a way we can add that?

**Mara**: I know the list isn't complete. The problems on the board are the ones Andy and I talked about earlier. And even some of these problems really need to get broken down into smaller tasks. I understand your concerns about code vulnerabilities. Andy, what do you think?

**Andy**: Right now, just getting a build out the door is hard. Let's start with some of the basic problems. I do like that we have a central place where we can keep track of our issues. We can add issues to the backlog and prioritize them once we're ready.

**Mara**: Before we add any issues, let's talk a bit more about what everyone is working on.

:::row:::
  :::column span="4":::
Each team member shares what they're working on and other concerns they have. As a brainstorming activity, they add sticky notes to a whiteboard. Their whiteboard fills up quickly.
  :::column-end:::
  :::column:::
![A whiteboard containing sticky notes](../media/3-whiteboard.png)
  :::column-end:::
:::row-end:::

Eventually the team settles on seven top issues. Andy volunteers to add tasks to Azure Boards while everyone watches. Here's what the board looks like.

![Backlog of tasks](../../shared/media/build-all-tasks.png)

**Amita**: Wow, that's a lot of problems. How are we ever going to fix all those?

**Mara**: We don't have to fix them all right away. For now, we've identified a _backlog_ or list of work we could pull from. When we plan work, we get to choose what's most urgent or important.

After some more discussion, the team decides to take on the three issues Mara originally proposed:

* **Stabilize the build server**
* **Create a Git-based workflow**
* **Create unit tests**

**Mara**: These seem like the easiest issues to take on, and they address some recent challenges that came up. Let's set up a project, a team, and a sprint. Then we can decide who does what.

Mara and the team are off to a good start. Next, you'll set up the project, team, and some tasks on Azure Boards.
