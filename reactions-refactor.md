# Reactions refactoring

## Short term

- [x] Simplify Amjuel reaction classes (Hydrogenic isotopes and He; iz, rec, cx)
  - [x] Rates from json files rather than static vars; similar to OpenADAS*
  - [x] Move duplicated code up from H_iz, H_rec, He_iz, He_rec $\to$ to AmjuelReaction
- [x] OpenADASReaction, AmjuelReaction inherit from common Reaction class
- [ ] Regression tests for hydrogenic iz, rec, cx rates and impurity iz, rec, cx rates


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
- [ ] Correctness tests

## RemKit approach

For each transition / reaction, set up terms for

### Density / number
$$
\begin{align}
\frac{\partial{n_s}}{\partial{t}} &= c_s R \prod_{s'{\in}I}{n_{s'}}
\end{align}
$$

where the product is over $I$, the set of input/reactant species and

 - $c_s$ is the population change
 - $n_{s'}$ the species density of species $s'$
 - $R$ is the reaction rate.

#### e.g. Ionisation of $C_{+3}$

$C_{+3} + e \to C_{+4} + 2e$

$c_s$ is:
|    $s$   | $c_s$ |
| -------- | ----- |
| $C_{+3}$ |  -1   |
| $C_{+4}$ |  +1   |
| e        |  +1   |

$$
\begin{align}
\frac{\partial{n_{C+3}}}{\partial{t}} &= -R n_{C+3} n_e \\
\frac{\partial{n_{C+4}}}{\partial{t}} &= R n_{C+3} n_e \\
\frac{\partial{n_{e}}}{\partial{t}} &= R n_{C+3} n_e \\
\end{align}
$$


### Momentum

$$
\begin{align}
\frac{\partial{G_s}}{\partial{t}} &= \frac{c_s w_s R G_s}{n_s} \text{ if } c_s < 0 \\
\frac{\partial{G_s}}{\partial{t}} &= - R \sum_{s'} \left(\frac{m_s}{m_{s'}} c_{s'} \frac{w_s w_{s'}}{w_{out}} \frac{G_{s'}}{n_{s'}}\right) \text{ if } c_s > 0 \\
\end{align}
$$

where the sum is over all reactants / instate species which themselves have a **+ve** pop. change.

 - $c_s$ is the population change
 - $w_s$ the participation weight
 - $G_s$ the species flux
 - $n_s$ the species density of species $s$
 - $w_{out}$ is the sum of participation weights over the products
 - $R$ is the reaction rate.

Note that:

- splittingFactor in the code is $w_{out}$
- $c_s$ currently labelled $S$; change
- $w_s$ term ignored for now
- $G_s$ gets incorporated later in H3 (I think)
- massActionFactor is the product of all the reactant densities
- When computing momentum changes for species with -ve pop changes:
  - Only the species itself contributes to the source term
- When computing momentum changes for species with +ve pop changes:
  - Reactants with -ve pop. changes (those being "consumed") contribute *+vely*
  - Reactants with +ve pop. changes (those being net generated) contribute *-vely*

#### e.g. Ionisation of $C_{+3}$

$C_{+3} + e \to C_{+4} + 2e$

${C+3}$ ($c_s < 0$) mom. source is
 
$$
\begin{align}
\frac{\partial{G_{C+3}}}{\partial{t}} &= R G_{C+3} \frac{- w_{C+3} }{n_{C+3}} \\
                                      &= -R M_{C+3} v_{C+3} \frac{w_{C+3} }{n_{C+3}} \\
\end{align}
$$
