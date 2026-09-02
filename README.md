Scheduling Logic & Cooperative Execution

The program uses a Round-Robin cooperative scheduler to manage multiple tea batches simultaneously.

    Quasi-Concurrency: Instead of true multithreading (where the OS forces threads to pause), this script uses cooperative execution. The central while loop acts as a scheduler, iterating through an active list of batches.

    Yielding Control: The scheduler cannot interrupt a batch. Instead, each batch voluntarily pauses and returns control to the main loop using coroutine.yield() after completing a specific processing stage (e.g., weighing, withering).

    Safe Resumption: Before resuming a batch, the scheduler checks coroutine.status() to ensure it does not attempt to resume a "dead" (completed) coroutine. This allows active batches (like one stuck in the reprocessing loop) to continue executing safely while finished batches are ignored.

Part (e): Coroutine State Persistence vs. Normal Functions

Requirement: Explain how coroutine state persistence simplifies the implementation compared with restarting a normal function.

When a normal Lua function returns, its execution stack is completely destroyed. All local variables and progress are lost. If we used normal functions for this pipeline, we would have to build a complex external state machine (using global variables or external database tables) just to remember whether a specific batch was at the "withering" or "drying" stage before calling the function again.

Coroutines fundamentally simplify this because they persist their state across yields. When a coroutine yields, its execution stack, local variables (like batchID and qualityPassed), and exact line of execution are preserved in memory. When the scheduler calls coroutine.resume(), the batch wakes up exactly where it left off. This localized memory allows us to write straightforward, sequential procedural loops without needing external tracking mechanics to manage the factory stages.
