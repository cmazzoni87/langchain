# LangChain Tracing

LangChain Plus helps you visualize, monitor, and evaluate LLM applications. To get started with local or hosted tracing, use one of the following guides.

- [Locally Hosted Tracing](../tracing/local_installation.md)
- [Cloud Hosted Tracing](../tracing/hosted_installation.md)

_Our hosted alpha is currently invite-only. To sign up for the wait list, please fill out the form [here](https://forms.gle/tRCEMSeopZf6TE3b6)._


## Saving Traces

Once you've launched the local tracing server or made an account and retrieved an API key to the hosted solution, your LangChain application will automatically log traces as long as
you set the following environment variables:

```bash
export LANGCHAIN_TRACING_V2="true"
# export LANGCHAIN_SESSION="my session name" # Otherwise, traces are stored in the "default" session
# export LANGCHAIN_ENDPOINT="https://api.langchain.plus" # Uncomment if using hosted server
# export LANGCHAIN_API_KEY="my api key" # Uncomment and add your API key generated from the settings page if using a hosted server
```

As long as these variables are correctly set, and the server is online, all your LangChain runs will be saved. You can interact with these traces in the UI or using the `LangChainPlus` client.

## Tracing UI Walkthrough

When you first access the LangChain Plus UI (and after signing in, if you are using the hosted version), you should be greeted by the home screen with more instructions on how to get started.
From here, you can navigate to the `Sessions` and `Datasets` pages. For more information on using datasets in LangChain Plus, check out the [Datasets](../tracing/datasets.md) guide.

Traces from your LangChain runs can be found in the `Sessions` page. A "default" session should already be created for you. 

A session is just a way to group traces together. If you click on a session, it will take you to a page that with no recorded runs.
You can create and save traces to new sessions by specifying the `LANGCHAIN_SESSION` environment variable in your LangChain application. You can check out the [Chainging Sessions](#changing-sessions) section below for more configuration options.

<!-- TODO Add screenshots when the UI settles down a bit -->
<!-- ![](../tracing/homepage.png) -->

If we click on the `default` session, we can see that to start we have no traces stored.

<!-- TODO Add screenshots when the UI settles down a bit -->
<!-- ![](../tracing/default_empty.png) -->

If we now start running chains and agents with tracing enabled, we will see data show up here.

<!-- To do so, we can run [this notebook](../tracing/agent_with_tracing.ipynb) as an example. After running it, we will see an initial trace show up. -->

<!-- TODO Add screenshots when the UI settles down a bit -->

From here we can explore the trace at a high level by clicking on the arrow to show nested runs.
We can keep on clicking further and further down to explore deeper and deeper.

<!-- TODO Add screenshots when the UI settles down a bit -->
<!-- ![](../tracing/explore.png) -->

We can also click on the "Explore" button of the top level run to dive even deeper.
Here, we can see the inputs and outputs in full, as well as all the nested traces.

<!-- TODO Add screenshots when the UI settles down a bit -->
<!-- ![](../tracing/explore_trace.png) -->

We can keep on exploring each of these nested traces in more detail.
For example, here is the lowest level trace with the exact inputs/outputs to the LLM.

<!-- TODO Add screenshots when the UI settles down a bit -->
<!-- ![](../tracing/explore_llm.png) -->


## Changing Sessions

1. To record traces to a session other than `"default"`, you can set the `LANGCHAIN_SESSION` environment variable to the name of the session you want to record to:

    ```python
    import os
    os.environ["LANGCHAIN_TRACING_V2"] = "true"
    os.environ["LANGCHAIN_SESSION"] = "my_session"
    ```

2. To switch sessions mid-script or mid-notebook, you have a few options:

    a. Explicitly pass in a new `LangChainTracer` callback

    ```python
    from langchain.callbacks.tracers import LangChainTracer
    tracer = LangChainTracer(session_name="My new session")
    agent.run("How many people live in canada as of 2023?", callbacks=[tracer])
    ```

    b. Use the `tracing_v2_enabled` context manager:

    ```python
    import os
    from langchain.callbacks.manager import tracing_v2_enabled
    os.environ["LANGCHAIN_SESSION"] = "my_session"
    # ... traces logged to "my_session" ...
    with tracing_v2_enabled("My Scoped Session Name"):
        # ... traces logged to "My New Session" ...
    ```

    c. Update the `LANGCHAIN_SESSION` environment variable (not thread safe)

    ```python
    import os
    os.environ["LANGCHAIN_SESSION"] = "my_session"
    # ... traces logged to 'my_session' ...
    os.environ["LANGCHAIN_SESSION"] = "My New Session"
    # ... traces logged to "My New Session" ...
    ```