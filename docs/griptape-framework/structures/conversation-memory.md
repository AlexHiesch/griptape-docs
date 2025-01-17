# Conversation Memory

Griptape supports different types of conversation memory for pipelines and agents.

By default, pipelines don't initialize memory, so you have to explicitly pass it to them:

```python
Pipeline(
    memory=ConversationMemory()
)
```

There are two other types of memory: `BufferConversationMemory` and `SummaryConversationMemory`. `BufferConversationMemory` will keep a sliding window of tasks that are used to construct a prompt:

```python
Pipeline(
    memory=BufferConversationMemory(buffer_size=3)
)
```

This works great for shorter pipelines but fails if the whole workflow context needs to be present. You can use `SummaryConversationMemory` to address that:

```python
Pipeline(
    memory=SummaryConversationMemory(
        summarizer=PromptDriverSummarizer(
            driver=OpenAiPromptDriver()
        ),
        offset=2
    )
)
```

This will progressively summarize the whole pipeline except for the last two tasks.

Finally, you can persist memory by using memory drivers. Griptape comes with one memory driver for automatically storing memory on disk. Here is how you can initialize memory with a driver:

```python
ConversationMemory(
    driver=DiskConversationMemoryDriver(file_path="memory.json")
)
```

To load memory:

```python
DiskConversationMemoryDriver(file_path="memory.json").load()
```

You can easily build drivers for your own data stores by extending `BaseConversationMemoryDriver`. You only need to implement `store` and `load` methods.