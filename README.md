# Food Journal Skills

Agent skills for maintaining a food-journal site built with Vite and React. The skills focus on bakery recipes, weekly meal prep, bilingual copy, photo handling, and safe food-content editing.

## Skills

### `food-journal`

Use this when adding or editing:

- a bakery recipe
- a weekly meal-prep entry
- bilingual English / Chinese food copy
- food photos, covers and galleries
- recipe cards, meal-prep cards, stats and prep steps

It documents the expected data shapes, the photo archive and optimization workflow, editorial voice, health-claim guardrails, and build checks.

## Install

From the Skills CLI:

```bash
npx skills add xtguan/skills@food-journal
```

Or install directly from GitHub:

```bash
npx skills add https://github.com/xtguan/skills
```

## Structure

```text
skills/
  README.md
  LICENSE
  food-journal/
    SKILL.md
```

## Using the skill

Once installed, ask your agent to use `food-journal` when working on recipe or meal-prep content. For example:

```text
Use the food-journal skill to add this week's meal prep.
```

The skill is written for a Vite + React project with content modules under `src/recipes` and `src/meal-prep`. Adapt the paths if your project has a different layout.

## Scope

This skill is about content and photo workflow, not about medical or nutritional advice. Recipe notes should describe what was actually cooked and should avoid unverified health claims.

## License

MIT. See [LICENSE](LICENSE).
