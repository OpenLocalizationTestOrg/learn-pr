Andy and Mara are excited to show Amita the progress they've made. They've already set up a dashboard. Now they can monitor pull requests and visualize the health of their builds.

In this unit, you add a widget to your dashboard to help visualize your test runs over time.

Amita takes a look and is excited.

**Amita**: This is great progress. Thank you! Not to sound ungrateful, but is there any way I can see just a brief overview of the test results over time?

**Mara**: Yes! Azure DevOps lets you add widgets to your dashboards. It only takes a few minutes. Let me show you.

## Add the widget to the dashboard

1. In your Azure DevOps project, select **Overview**, and then select **Dashboards**.

    > [!NOTE]
    > If you ran the template to create the Azure DevOps project, you won't see the dashboard widgets you set up in previous modules.

1. Select **Edit**.
1. In the **Add Widget** pane, search for **Test Results Trend**.
1. Drag **Test Results Trend** to the canvas.
1. Select the gear icon to configure the widget.

    a. Under **Build pipeline**, select your pipeline.

    b. Keep the other default settings.
1. Select **Save**.
1. Select **Done Editing**.

Although the widget displays only one test run, you now have a way to visualize and track test runs over time. Here's an example that shows a few successful test runs.

![The Test Results Trend widget displaying passing tests](../media/5-test-results-trend-widget.png)

If you begin to see test failures, you can click a point on the graph to navigate directly to that build.
