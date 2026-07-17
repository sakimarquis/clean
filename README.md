# clean: helping your inner code neat freak (hopefully)

I don't think "completing a task" is just about producing code that passes. Some models will do whatever it takes to get the task done. They patch things together, bypass the architecture, and eventually produce something that works.

To me, architecture (or abstraction) is a strong regularizer. Humans naturally reason along the same conceptual scaffold, which is why our behavior is remarkably consistent.

Many models—even very successful ones like Codex—still don't seem motivated to preserve abstraction. They can reach the right outcome, but the process rarely follows a coherent architecture.

When a great engineer (or model) writes code or proposes a plan, I can usually tell at a glance whether it's what I wanted. With some models, the code is much harder to reason about. There's a lot of hidden magic, random dataclasses, `if size < 0`, `def do_it(): return _do_it()`, tests like `assert 0 < 1`, and countless tiny wrappers that don't really encode any concept. It feels stitched together from local fixes instead of being driven by a single design. The repository slowly turns into Frankenstein's monster.

My code agent has spent a long time treating my repository as a design exploration playground. Whenever it's uncertain (and of course it is), it materializes that uncertainty into another dataclass, wrapper, config axis, registry, or test. Then I end up being the one who has to compress all of those concepts back into a simpler abstraction afterward. That's probably the part I find most frustrating.

I'm trying one more thing: turning what I want into a Skill. So far it's been working well for me as a lightweight helper. It won't solve everything, but it quickly points out the ugliest parts of the code with only a small false-positive rate. Hopefully you'll find it useful too.
