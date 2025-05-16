# Reactions refactoring

## Short term

- [x] Simplify Amjuel reaction classes
  - [x] Rates from json files rather than static vars; similar to OpenADAS*
  - [x] Duplicated code from H_iz, H_rec, He_iz, He_rec $\to$ to AmjuelReaction
- [x] OpenADASReaction, AmjuelReaction inherit from Reaction
- [ ] Regression tests?
- [ ] Correctness tests?

## Longer term

New Reaction class, loosely based on Stefan's Mijin's new RemKit module
- [x] ReactionParser as member var
  - [x] Extracts species from reaction string
  - [x] Determines stoichiometry matrix values
  - [ ] ReactionParser unit tests
- Subclasses implement methods to retrieve reaction, radiation coeffs
- Reaction::transform() replaces all subclass transform() funcs, sets up (for each species in stoichiometry matrix):
  - [ ] Particle num / dens sources
  - [ ] Mom sources
  - [ ] Energy sources
