# Evaluating AI Agents

Guidelines for testing and evaluating AI agents in production, based on [Anthropic's engineering guide](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) and [omarsar0's summary](https://x.com/omarsar0/status/2009727896706003043).

## Why Evals Matter

The capabilities that make AI agents useful (autonomy, intelligence, flexibility) are the same ones that make them hard to evaluate. You can't just run unit tests and expect your agentic app to work.

Good evaluations help teams:
- Ship AI agents more confidently
- Catch issues before they affect users
- Adopt new models faster when they're released
- Resolve ambiguity about how AI should handle edge cases

## When to Build Evals

Build evals at the **start** of agent development to explicitly encode expected behavior. Two engineers reading the same spec could interpret edge cases differently - an eval suite resolves this ambiguity.

Teams with evals can adopt new models in days. Teams without evals face weeks of manual testing.

## Types of Evaluations

### Capability Evals
- Ask "what can this agent do well?"
- Start at low pass rates
- Used to push boundaries and discover limitations

### Regression Evals
- Ask "can it still handle previous tasks?"
- Should stay near 100%
- Tasks graduating from capability to regression represent real progress

## Three Types of Graders

Each has trade-offs:

| Grader Type | Pros | Cons |
|-------------|------|------|
| **Code-based** | Fast, cheap, reproducible | Brittle to valid variations |
| **Model-based** | Handles nuance and open-ended tasks | Non-deterministic, requires human calibration |
| **Human** | Gold-standard quality | Expensive and slow |

Combine all three for robustness.

## Handling Non-Determinism

Two metrics matter:

- **pass@k**: Probability of at least one success in k attempts
- **pass^k**: Probability that all k trials succeed

These diverge dramatically. At k=10, pass@k can approach 100% while pass^k falls to near zero.

## Practical Recommendations

1. **Start small**: Begin with 20-50 simple tasks drawn from real failures
2. **Convert manual checks**: Turn checks you already perform into test cases
3. **Grade outputs, not paths**: Don't penalize different approaches that reach the same result
4. **Include partial credit**: Complex tasks rarely have binary success/failure
5. **Early changes have large effects**: Small sample sizes suffice initially

## Common Pitfalls

- Rigid grading that penalizes equivalent but differently-formatted answers
- Ambiguous task specifications
- Stochastic tasks impossible to reproduce
- Grading the path taken rather than the outcome

## Real-World Example: Descript

Descript's agent helps users edit videos. They built evals around three dimensions:

1. **Don't break things** - Safety and stability
2. **Do what I asked** - Task completion accuracy
3. **Do it well** - Quality of execution

They evolved from manual grading to LLM graders with criteria defined by the product team and periodic human calibration.

## Resources

- [Anthropic: Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [omarsar0's Summary Thread](https://x.com/omarsar0/status/2009727896706003043)
