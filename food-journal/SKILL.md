---
name: food-journal
description: "Maintain the bakery and weekly meal-prep sections of a Vite + React food-journal site. Use when adding or editing a bakery recipe, a meal-prep week, food photos, bilingual recipe copy, or the associated cards and galleries. Covers data schemas, photo pipeline, editorial voice, health-claim guardrails, and build checks."
argument-hint: "[bakery-recipe | meal-prep-week | photo-update]"
metadata:
  author: food-journal
  version: "1.0.0"
---

# Food Journal

This skill keeps food sections consistent in a Vite + React food-journal site. Bakery and meal-prep content are data modules in `src/recipes/` and `src/meal-prep/`, rendered by `src/components/Bakery.jsx` and `src/components/MealPrep.jsx`. Adapt the paths if your project uses a different structure.

## When to use this skill

Use for:

- Adding a bakery recipe or updating Recipe 01/02/etc.
- Adding a weekly meal-prep entry.
- Moving, optimizing, or captioning food photos.
- Editing bilingual bakery/meal-prep copy.
- Changing a recipe card, meal-prep cover, gallery, or stats.
- Checking that a food-content change builds and renders.

Do not use for general blog posts, project case studies, or gallery albums. Use the normal site conventions for those.

## Non-negotiable rules

1. Preserve the source facts. Brands, quantities, times, cooking order, dish counts, and photo ownership come from the brief. Do not invent precise numbers, temperatures, or health outcomes.
2. Keep both languages in sync. Bakery and meal-prep content has `content.en` and `content.zh`. Update both.
3. Use every supplied photo. Originals are archived; optimized copies are served. A cover photo counts as used. Caption every gallery photo in both languages.
4. Do not use em dashes (`—`). Use a normal hyphen (`-`). Keep the house style consistent.
5. Do not make unverified health claims. Avoid "zero sugar spike" and similar. Prefer "lower sugar", "smaller portion", "without the sugar crash" only when the note says it is not a zero-spike food. Add a portion/medical caveat where relevant.
6. Keep the established section names and voice: **The Corporate Captive's Kitchen & Bakery** and **Clocked Out & Cooked Up**. Do not rename sections unless asked.
7. Do not add a new content field without updating the component that renders it.

## Repo map

```text
src/
  components/Bakery.jsx       bakery list/detail, language toggle, print
  components/MealPrep.jsx     weekly list/detail, language toggle
  recipes/                    one module per bakery recipe + index.js
  meal-prep/                  one module per week + index.js
  assets/bakery/              served bakery images
  assets/meal-prep/           served meal-prep images
source-photos/
  bakery/                     bakery originals
  meal-prep/                  meal-prep originals
scripts/generate-sitemap.mjs  sitemap generation
```

Registries sort by `date` newest-first. Adding a newer recipe/week shifts card order and recipe numbers (Recipe 01, 02, ...). That is expected. Update any tests or copy that assumes numbering.

## Bakery recipe workflow

Create `src/recipes/<slug>.js`, import the thumbnail/photos, and register it in `src/recipes/index.js`.

Use this shape:

```js
export default {
  slug: 'kebab-case-slug',
  date: 'YYYY-MM-DD',
  category: 'short-category',
  thumbnail,
  photos: [{ src, caption: { en: '...', zh: '...' } }],
  content: {
    en: {
      categoryLabel: 'Badge label',
      title: 'Recipe title',
      subtitle: 'Article-style subtitle',
      summary: 'Card summary',
      intro: 'First-person story',
      stats: [{ label: 'Yield', value: '...' }],
      ingredients: [{ title: 'Group', items: [{ amount, name, note }] }],
      steps: [{ title: 'Step', body: ['Paragraph'], tip: 'Optional' }],
      notes: [{ title: 'Note', body: '...' }],
    },
    zh: {
      categoryLabel: '徽章',
      title: '标题',
      subtitle: '副标题',
      summary: '卡片摘要',
      intro: '第一人称故事',
      stats: [{ label: '份量', value: '...' }],
      ingredients: [{ title: '分组', items: [{ amount, name, note }] }],
      steps: [{ title: '步骤', body: ['段落'], tip: '可选' }],
      notes: [{ title: '笔记', body: '...' }],
    },
  },
}
```

Bakery conventions:

- `thumbnail` is the card/hero image.
- `photos` are extra detail-page images. Do not repeat the thumbnail in the gallery unless requested.
- Recipe order is date-driven. An older recipe can become Recipe 03/04 after a new one is added.
- Keep honest failures in the copy. "The crumble didn't crisp" is better than pretending it did.
- Use the established first-person, dry-humour voice. Avoid generic AI phrasing and unnecessary superlatives.
- If a recipe is not actually baked (e.g. truffles), say so in the copy even though it lives in the bakery section.

## Meal-prep workflow

Create `src/meal-prep/week-YYYY-MM-DD.js`, then register it in `src/meal-prep/index.js`.

Use this shape:

```js
export default {
  slug: 'week-N',
  date: 'YYYY-MM-DD',
  thumbnail,
  photos: [{ src, caption: { en: '...', zh: '...' } }],
  content: {
    en: {
      weekLabel: 'Week N · Month D-D, YYYY',
      title: 'Week N: ...',
      subtitle: '...',
      summary: '...',
      intro: '...',
      sugarNote: 'Portion/medical caveat.',
      stats: [{ label: 'Prep day', value: '1' }],
      planTitle: 'The week at a glance',
      meals: [{ title: '...', note: '...', items: ['...'] }],
      dishesTitle: 'What I cooked',
      dishes: [{ name: '...', detail: '...' }],
      prepTitle: 'The prep day',
      prep: [{ title: '...', body: '...' }],
      galleryTitle: '...',
      storageTitle: 'Storage and reheating',
      storage: ['...'],
      notesTitle: 'Notes from the kitchen',
      notes: [{ title: '...', body: '...' }],
      closing: '...',
    },
    zh: { /* same shape */ },
  },
}
```

Meal-prep conventions:

- The week label is the week the food covers, not necessarily the prep date. Use the date the week starts for sorting.
- State the prep day and any time window supplied in the brief (for example "Saturday, 7:30-11:30").
- Count the cooked dishes in `stats` and list them in `dishes`. Include staples (rice, kimbap), dessert, and fresh sides separately when they are not part of the count.
- `meals` can be Breakfast/Lunch/Dinner when a menu is supplied. If there is no strict menu, use component cards (Egg bites / Mains / Sides) and say so in the intro.
- Preserve the actual cooking order, especially oven-heavy days. If an order is supplied, write it as separate `prep` steps, not one long paragraph.
- Include a cleanup step when requested: hand-wash non-dishwasher-safe pots/utensils/bowls, dishwasher the rest, dry and return tools to drawers, clear the counter.
- Do not invent a meal plan that was not provided. Components with a note are honest.

## Photo pipeline

1. Find the new originals in the supplied folder.
2. Decide ownership:
   - bakery-only or shared bakery/meal-prep photos -> `source-photos/bakery/`
   - meal-prep-only photos -> `source-photos/meal-prep/`
3. Move the originals into the archive. Do not leave duplicates in the supplied folder.
4. Optimize to `src/assets/bakery/` or `src/assets/meal-prep/`:
   - max dimension 1600 px
   - JPEG quality 82
   - progressive, optimized
   - descriptive kebab-case filenames
5. Import the optimized assets into the data module. Do not reference `source-photos/` at runtime.
6. Use every photo:
   - one cover/thumbnail
   - remaining photos in the gallery, each with EN/ZH captions
7. When changing a cover:
   - move the old cover into the gallery or remove it only if it is already used elsewhere
   - add the new cover asset
   - keep the gallery count consistent
8. Remove the empty supplied folder after moving everything.
9. If a photo is shared by a bakery recipe and a meal-prep week, import the same optimized asset in both files rather than duplicating the file.

Never generate fake food photos when real photos are supplied.

## Editorial rules

- First person, practical, specific. Mention what worked and what did not.
- Keep the "corporate slave / wage slave" humour, but do not overdo it.
- Use real quantities and brands when supplied: `Great Value`, `E.D. Smith`, `Farm Boy`, `H Mart`, `Biscoff`, etc.
- Do not add em dashes. Check new files for `—`.
- Avoid "zero sugar spike", "guilt-free", "clean eating", and similar claims unless explicitly confirmed and the copy includes a caveat.
- For a lower-sugar recipe, say what is lower and what is not. Example: "No Sugar Added filling and sweetener help, but the shells are still pastry."
- Do not silently rewrite the source recipe into a different dish. If improving the copy, keep the ingredients and method.
- Health-related notes end with a non-medical-advice caveat when appropriate.
- Do not rename a section, card, or URL slug unless asked. Slug changes break deep links.

## Validation

Run:

```bash
node scripts/generate-sitemap.mjs
node node_modules/vite/bin/vite.js build
git diff --check
```

If the dev server is needed:

```bash
node node_modules/vite/bin/vite.js --host 127.0.0.1 --port 5173 --strictPort
```

There is no test runner configured in the repo. Use the Playwright `webapp-testing` skill or the existing CDP screenshot pattern to check:

- card counts and newest-first order
- recipe/week deep links: `?recipe=<slug>#bakery`, `?mealprep=<slug>#meal-prep`
- gallery photo count
- ingredient/step/note counts
- cover photo and section titles
- Chinese toggle
- 1440 px desktop and 390 px mobile
- page errors

Before handing off, list changed files, new photo names, gallery/cover counts, and build/check results.

## Checklist

- [ ] Both EN and ZH content updated
- [ ] New recipe/week registered in its `index.js`
- [ ] Date and sorting are correct
- [ ] Every supplied photo is used
- [ ] Cover and gallery do not accidentally duplicate or hide a photo
- [ ] Every gallery photo has EN/ZH captions
- [ ] Dish count, stats, and `dishes` array agree
- [ ] Prep order matches the supplied account
- [ ] Cleanup step included when requested
- [ ] No em dashes
- [ ] No unverified health claims
- [ ] Build passes
- [ ] Desktop/mobile render and deep links checked
