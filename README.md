# e2e-playerwright-axe-audit-reports
Show how to generate baseline audits by importing route paths and looping through for initial scan, as router paths grows, coverage will also grow automatically.

## Include all tags, including experimental
```javascript
const failingTags = ['wcag2a', 'wcag2aa', 'wcag21a', 'wcag21aa', 'wcag22a', 'wcag22aa'];
const feedbackTags = ['wcag2aaa', 'best-practice', 'experimental'];
const allTags = failingTags.concat(feedbackTags);
const results = await axeBuilder
  .withTags(allTags)
  .analyze();
 ```

For reference here are the current rules of [WCAG 2.x AAA](https://dequeuniversity.com/rules/axe/4.8/#wcag-2x-level-aaa-rules),  [best-practice](https://dequeuniversity.com/rules/axe/4.8/#best-practice-rules) and [experimental](https://dequeuniversity.com/rules/axe/4.8/#experimental-rules). Experimental used to be nine rules but two of them graduated, adding experimental is away to stay ahead of what's coming.

## How to fail on certain tags and just report warnings on other tags
```javascript
if (results.violations.length) {
  const violationsByTag = {};
  const warnings = {}

  results.violations.forEach(violation => {
    violation.tags.forEach(tag => {
      if (!allTags.includes(tag)) return;
      if (!violationsByTag[tag]) {
        violationsByTag[tag] = [];
      }
      violationsByTag[tag].push(violation);
      Object.keys(violationsByTag).forEach(tag => {
        if (failingTags.includes(tag)) {
          console.error(`${tag} ${violationsByTag[tag].length} violations`);
          expect(violationsByTag[tag].length).toEqual([]);
        } else {
          warnings[tag] = violationsByTag[tag];
        }
      })
      if (Object.keys(warnings).length) {
        console.warn(warnings);
      }
    });
  });
}
```

## Utilize an html reporter, so you can view and revisit findings without having to rerun
```javascript
createHtmlReport({
  results,
  options: {
    projectKey: route.name.replace(/\s+/g, '-').toLowerCase(),
    outputDir: 'reports/[whatever-app][optionally-version-#]',
    reportFileName: `${route.name}.html`,
  },
});
```

## If you support multiple themes, such as dark and light
```javascript
// if already looped through one theme, on additionaly themes
// it's recommended you scope to just rules checking color, to optimize
const results = await axeBuilder
  .withTags([
    'cat.color'
  ])
  .analyze();
 ```

For reference here are the current rules of [WCAG 2.x AAA](https://dequeuniversity.com/rules/axe/4.8/#wcag-2x-level-aaa-rules),  [best-practice](https://dequeuniversity.com/rules/axe/4.8/#best-practice-rules) and [experimental](https://dequeuniversity.com/rules/axe/4.8/#experimental-rules). Experimental used to be nine rules but two of them graduated, adding experimental is away to stay ahead of what's coming.
