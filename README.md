# cubew_tutorial

Tutorial data (LBL method) for the [cubew](https://itachiwalker.github.io/cubew/) app ([source code](https://github.com/itachiwalker/cubew)) — a 3x3 cube puzzle simulator.

The data in this repository is published under the **MIT License**. Feel free to fork it and modify/redistribute it as you like.

---

## What's in this repository

```
default.json     # The tutorial content itself (definitions of levels, steps, lessons/exams)
LBL/*.png        # Hint images and goal diagrams used at each step of the LBL method
terms/*.png      # Images used for glossary/terminology explanations
schema.md / schema.ja.md   # Schema/format spec for default.json (English / Japanese)
```

See [schema.md](./schema.md) for how to write `default.json` (field meanings, `moves` notation, how to reference images, etc).

---

## How it's used

The `default.json` and image files in this repository are bundled directly into the [cubew](https://itachiwalker.github.io/cubew/) app ([source code](https://github.com/itachiwalker/cubew)). The app's "Tutorial" feature (the 📚 button) displays step-by-step lessons and exams based on this data.

Currently, the cubew app **only works by bundling the data from this repository directly**. Support for loading external tutorial data from an arbitrary URL is planned for a future release (once that's added, you'll be able to fork this repository, build your own tutorial, and publish/use it as-is).

---

## Contributing

- If you find typos, unclear explanations, or errors in the steps, please let us know via an Issue or Pull Request
- Suggestions for new levels, steps, or lessons are welcome too. Please edit `default.json` following the spec in [schema.md](./schema.md) and send a Pull Request
- If you add or replace images (`LBL/`, `terms/`), please remember to also update the corresponding paths in `default.json`

---

## License

MIT License. See the `LICENSE` file in this repository for details.
