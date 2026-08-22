# Musk Algorithm: an Agent Skill

An [Agent Skill](https://agentskills.io) that makes your AI coding assistant run Elon Musk's five-step algorithm before it plans, optimizes, or automates anything:

1. **Question every requirement** (each one needs a named owner and observed evidence)
2. **Try to delete it** (if you never add ~10% back, you didn't delete enough)
3. **Simplify and optimize** (only what survived deletion)
4. **Accelerate cycle time** (only what deserves to exist)
5. **Automate** (last, always)

> "The most common mistake of smart engineers is to optimize a thing that should not exist."

AI assistants fail exactly the way Musk describes smart engineers failing. Ask one to plan a fully specified feature and it opens with "your plan is sound," keeps every component you named, and starts optimizing them. This skill forces the questioning and deletion passes first, in order, and gives the model explicit counters for the rationalizations it uses to skip them.

## What changes in practice

Tested on the same request with and without the skill (a pipeline feature with five specified components, including an LLM judge and a scheduled automation):

- **Without:** the assistant kept all five components, refined each one, and hardened the automation.
- **With:** it deleted four of the five (the human already reviewed the output by hand, so the judge was scoring a decision the human was making anyway), kept the one component that addressed the actual problem, and refused the automation until the manual loop proved itself.

## Install

**Claude Code (personal, all projects):**

```bash
git clone https://github.com/fomoPhil/musk-algorithm.git ~/.claude/skills/musk-algorithm
```

**Claude Code (single project):**

```bash
git clone https://github.com/fomoPhil/musk-algorithm.git .claude/skills/musk-algorithm
```

Any other tool that supports the Agent Skills spec: place this folder in that tool's skills directory.

## Use

It triggers automatically when you propose a feature, process, or automation, or you can invoke it directly:

```
run the algorithm on my plan for the notifications system
```

The output leads with a verdict (what should exist, what got deleted), one finding per step, and a single next action.

## What's inside

- `SKILL.md`: the five steps as enforceable instructions, with strict ordering, requirement ownership, the 10% add-back rule, red flags, and a rationalization table
- `references/stories.md`: the source quotes and case stories (fiberglass mats, the orphan sensor, radar deletion, falcon-wing doors, Model 3 over-automation) the model can load for ammunition when pushing back

## Credits

The algorithm is Elon Musk's, as told in the Everyday Astronaut Starbase interview and Walter Isaacson's biography *Elon Musk*. Quote compilation drawn in part from a thread by [@EricJorgenson](https://x.com/EricJorgenson). This repo just packages it so an AI agent actually follows it, in order.

## License

MIT
