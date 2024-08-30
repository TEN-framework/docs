# Life Cycle

The relationship between several lifecycle stages and their connection to sending messages:

- on_init ~ on_init_done: Handles its own initialization; cannot send or receive messages.

After everyone has completed on_init_done, they will collectively move into on_start.

- on_start ~ on_start_done: Can send messages and receive the results of sent messages, but cannot receive other messages. Since properties are initialized within on_start, you can perform initialization actions that depend on these properties being set up. However, as it's still in the initializing phase, it won't receive messages initiated by others, avoiding the need for various checks. You can actively send messages out, though.

- After on_start_done ~ on_stop_done: Normal sending and receiving of all messages and results.

After everyone has completed on_stop_done, they will collectively move into on_deinit.

- on_deinit ~ on_deinit_done: Handles its own de-initialization; cannot send or receive messages.
