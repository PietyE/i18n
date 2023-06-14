Small and type-safe package to create multi-language interfaces.

Features

- Base i18n functionality (interpolation, rich text, pluralization).
- Full TS support. They types are inferred, no need to write anything by hand.
- React hooks and components.
- Ability to load translations asynchronously (from the server or by lazy loading).
- Ability to work with plurals format that is comfortable for you.

## Usage

### Basic example

```ts
import { I18N, createPluralize } from "@ayub-begimkulov/i18n";
import en from "./keys/en.json";
import ru from "./keys/ru.json";

const pluralizeEn = createPluralize("en");
const pluralizeRu = createPluralize("ru");

const i18n = new I18N({
  defaultLang: "en",
  languages: {
    en: {
      keyset: en,
      pluralize: pluralizeEn,
    },
    ru: {
      keyset: ru,
      pluralize: pluralizeRu,
    },
  },
});

i18n.get("greeting"); // Hello
```

### Using with React

```ts
// i18n.ts
import {
  I18N,
  createPluralize,
  useTranslate as useTranslateBase,
  useI18n as useI18nBase,
} from "@ayub-begimkulov/i18n";
import en from "./keys/en.json";
import ru from "./keys/ru.json";

const pluralizeEn = createPluralize("en");
const pluralizeRu = createPluralize("ru");

const i18n = new I18N({
  defaultLang: "en",
  languages: {
    en: {
      keyset: en,
      pluralize: pluralizeEn,
    },
    ru: {
      keyset: ru,
      pluralize: pluralizeRu,
    },
  },
});

export const useTranslate = useTranslateBase<typeof i18n>;
export const useI18n = useI18nBase<typeof i18n>;

// index.tsx
import { I18NProvider } from "@ayub-begimkulov/i18n";
import { i18n } from "./i18n";

// ...

root.render(
  <I18NProvider i18n={i18n}>
    <App />
  </I18NProvider>
);

// component.ts
import { useTranslate } from "./i18n";

const Component = () => {
  const translate = useTranslate();

  return <div>{translate("some_key")}</div>;
};
```

## Reference

### `I18N`

Class that is responsible for loading/storing translations and updating language. All the react functionality leverages `I18N` API to update components whenever needed.

Example:

```ts
import { I18N, createPluralize } from "@ayub-begimkulov/i18n";
import en from "./keys/en.json";
import ru from "./keys/ru.json";

const pluralizeEn = createPluralize("en");
const pluralizeRu = createPluralize("ru");

const i18n = new I18N({
  defaultLang: "en",
  languages: {
    en: {
      keyset: en,
      pluralize: pluralizeEn,
    },
    ru: {
      keyset: ru,
      pluralize: pluralizeRu,
    },
  },
});

i18n.get("greeting"); // 'Hello'
```

### `useI18n`

A hook that returns an object with properties/methods of the i18n. Updates a component whenever the language changes.

Example:

```tsx
const allLanguages = ["en", "ru", "ar"];

const Component = () => {
  const { lang, setLang } = useI18n();

  return (
    <>
      <div>{lang}</div>
      {allLanguages.map((lang) => (
        <div onClick={() => setLang(lang)}>Select {lang}</div>
      ))}
    </>
  );
};
```

### `useTranslate`

A hook that returns a translate function. The component that uses this hook will update whenever the language changes.

Example:

```tsx
import { useTranslate } from "@ayub-begimkulov/i18n";

const Component = () => {
  const t = useTranslate();

  return <div>{t("welcome")}</div>;
};
```

### `createPluralize`

Creates a pluralize function for a given locale that will return a plural format for a specific number of items. Leverages [`Intl.PluralRules`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/PluralRules) under the hood.

Example:

```ts
import { createPluralize } from "@ayub-begimkulov/i18n";

const pluralizeEn = createPluralize("en");

pluralizeEn(1); // 'one'
pluralizeEn(0); // 'other'
```

### `I18NProvider`

A wrapper around React context provider. Used to share `I18N` instance across the components. Mostly used inside of the library hooks (`useI18n`/`useTranslate`).

Example:

```tsx
import { I18NProvider } from '@ayub-begimkulov/i18n';
import { App } from './App';
import { i18n } form './i18n';

root.render(
  <I18NProvider i18n={i18n}>
    <App />
  </I18NProvider>
)
```

### `TaggedText`

A component that allows to use React component inside of your translations.

Example:

```tsx
import { TaggedText, useTranslate } from "@ayub-begimkulov/i18n";

export const Component = () => {
  const t = useTranslate();

  console.log(t("key")); // "<1>Important!</1> Check out <2>the project documentation</2> before using this library"

  return (
    <div>
      <TaggedText
        text={t("key")}
        tags={{
          1: (text) => <strong>{text}</strong>,
          2: (text) => <a>{text}</a>,
        }}
      />
    </div>
  );
};
```