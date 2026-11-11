# Xuan Liu — Blog

Code, data and notes behind the posts on my [research blog](https://xuanl17.github.io/blog.html).

## Structure

Each post has its own folder under `posts/`. Published posts are named `YYYY-MM-<slug>`; drafts use the plain `<slug>` until they are published.

```
posts/
  <YYYY-MM-slug>/
    README.md        # title, date, links to the published post (EN / 中文),
                     # project page, paper and code
    post.md          # draft text (only while the post is being written)
    experiments/     # code, prompts and results, for my own experiments (lab posts link to the lab repo)
      <name>/
        README.md
        DESIGN.md
```

- If a post reports its own experiments, they live in `experiments/` inside the post folder.
- If the work behind a post already has a project page, paper or code repo, the post's `README.md` links there instead of copying it.
- The published text lives on the website; this repo does not duplicate it.

For posts written with [AISmithLab](https://github.com/AISmithLab), the experiments and code live in the lab's [blog repo](https://github.com/AISmithLab/blog); this repo keeps the post and links there.

## Posts

| Date | Post | Experiments / project |
|---|---|---|
| Draft | [What are we simulating when we simulate with an LLM?](posts/what-are-we-simulating/) | [AISmithLab/blog](https://github.com/AISmithLab/blog/tree/main/posts/what-are-we-simulating/experiments) |
| 2026-03-10 | [Making Human Studies as Reproducible as Open-Source Code](posts/2026-03-reproducible-human-studies/) | [HumanStudy Hub](https://humanstudy-hub.com/) |
| 2025-09-23 | [Calibrating Agents for Quantifiable, Reproducible Behavior](posts/2025-09-calibrating-agent-behavior/) | [CoBRA](https://cobra.clawder.ai/) |
| 2025-09-16 | [Can Implicit Language Reliably Control Agent Behavior?](posts/2025-09-implicit-agent-behavior/) | [CoBRA](https://cobra.clawder.ai/) |
