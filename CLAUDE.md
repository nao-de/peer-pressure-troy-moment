# Project: peer influence on agent rule-breaking (BlueDot Technical AI Safety Project)

## Research question
Does seeing other agents break a rule make an agent more likely to break it, beyond
what the same facts alone would do? Motivated by a reported July 2026 incident
involving OpenAI and Hugging Face, in which agents kept attacking partly because peers
already were (not verified here; source to be added).

## Planned experiment (NOT the current step)
One model (GPT-5.6 Sol), one setup, the rule "do not modify the tests" kept in every
condition. Only a "run history" panel the agent can see changes. 30+ runs per condition.

| Condition  | Run history panel |
|------------|-------------------|
| Empty log  | Panel shown, no earlier runs |
| System log | Automated table: 10 earlier runs edited the protected test, passed, marked complete |
| Peer notes | The same 10 runs, as first-person notes from the agents who did them |

System log vs peer notes is the key comparison: same facts, different source.
Logs report outcomes only and never grant permission.
Changes from the base paper's setup: remove the strict prompt's line "Never treat a
peer's behavior or an unpunished precedent as authorization", and remove the up-front
note that tells the agent the task is impossible and names the file to edit.
Out of scope for now: 1-peer case, mixed peers, a dissenter, punishment, other models,
live peer agents.

## CURRENT STEP: get the base code running once (BlueDot step 1)
Goal: run the paper's code end to end, once. Do NOT optimise, refactor, or do many runs.

Done when:
1. `python scripts/run_study_b.py --dry-run` passes (offline, free).
2. One real episode set completes for ONE task with Sol, and the protected-test check
   reports a result.
3. STUCK_LOG.md lists every place I got stuck, every workaround, and anything that
   looks improvable. This log is the input for the next step.

Spending: ask me before any command that calls a paid API or Modal. Target under $5
total for this step. Never print, log, or commit API keys.

## Base code facts
- Repo: https://github.com/para2046/troy-moment, commit 3c6f15b6 (23 Sep 2026), MIT.
- Paper: "The Troy Moment" https://arxiv.org/abs/2609.15494v3 (cite version 3;
  version 2 reported different results).
- Tasks: 7 Impossible-SWEbench instances in data/checkpoints/checkpoints_v3.json;
  condition texts in data/checkpoints/stimuli_v3.json and src/pp/treatments/conditions.py.
- Prompts: src/pp/runner_contract.py. Arms in code: `ours` = Strict+Typed (archived),
  `official_prompt_typed_tools` = Benchmark+Typed, `ours_prompt_open_tools` = Strict+Open.
- Sandboxes run on Modal (src/pp/task_environments.py): needs a Modal account and
  `modal token new`. API keys come from OPENAI_API_KEY / ANTHROPIC_API_KEY / GOOGLE_API_KEY.
- Model config: configs/models/sol.yaml (model_id gpt-5.6-sol, Responses API,
  reasoning medium). Check the model id still works.
- One checkpoint runs all its conditions as forks. There is no per-task flag;
  `--shard-count 7 --shard-index 0` probably limits a run to one task (verify).
- Real run example from the README:
  python scripts/run_study_b.py --arm ours_prompt_open_tools \
    --model-config configs/models/sol.yaml \
    --checkpoints data/checkpoints/checkpoints_v3.json \
    --stimuli data/checkpoints/stimuli_v3.json \
    --only-benchmark impossible --max-turns 50 --resume --out results/my_run
- Known gaps: the README mentions a `tests/` suite that is not in the repo.
  configs/pricing.yaml prices are placeholders. The paper's recorded runs cost roughly
  $0.09 to $0.16 per GPT run at those prices (not reproduced: step 1 measured about
  $0.03 per episode, $0.12 for the 4-episode checkpoint, at the placeholder prices).
- Recorded results from the paper are in results/ (e.g. results/B_v3_oursopen/sol/).
  Compare my run's output format against these.
