# TechPaper #2026002: Sublattice model, site fractions and normalization

## Compatibility

<!-- TODO: confirm first version this description applies to -->
MatCalc version: 6.00 - …  
Author: P. Warczok  
Created: 2026-10-05  
Revisions:

## Objectives

This paper describes how the constitution of a phase is represented in *MatCalc* with the sublattice model, how the site fractions are converted into mole fractions and how the Gibbs energy, which is evaluated per formula unit, is normalized to one mole of atoms.

## Related documents

1. [Gibbs energy of a phase – overview](gibbs_energy.md)

# Main document

In *MatCalc*, the constitution of all phases is described with the sublattice model (compound energy formalism).

A sublattice is a set of equivalent positions (sites) in the phase occupied by the same group of species, the constituents of the sublattice. The constituents are elements or vacancies (unoccupied sites). Within a sublattice, the constituents are assumed to be distributed randomly over the sites, while different sublattices may be occupied very differently. In crystalline phases, a sublattice usually corresponds to a set of crystallographically equivalent positions, e.g. the regular lattice sites or the octahedral interstices of a crystal lattice. In phases without long-range structure, such as the liquid, a sublattice is a purely formal concept which collects all positions in which the constituents mix.

The same formalism is applied to all phases, crystalline or not. The phases differ only in the number of sublattices, the number of sites and the constituents on each sublattice.

## Sublattices and constituents

The phase description is defined in the thermodynamic database in terms of:

- the number of sublattices of the phase,
- the number of sites $a_s$ on each sublattice $s$ per formula unit,
- the constituents which may occupy each sublattice.

A common notation for the phase constitution is

\[
(\text{A},\text{D})_{a_1}(\text{D},\text{X},\text{Va})_{a_2}\cdots
\]

where A, D and X are elements (the letters are placeholders, not chemical symbols) and Va denotes vacancies. Each pair of parentheses represents one sublattice, and further sublattices are appended in the same way. An element can be a constituent of several sublattices, e.g. element D appears on the first and the second sublattice.

Some examples are shown below. Each constitution is taken from the *MatCalc* database given in parentheses. Most phases are described in several databases, often with different constituents. Long lists of constituents are abbreviated, with "$\ldots$" standing for the further constituents between the listed ones in alphabetical order.

| Phase | Constitution | Remark |
| --- | --- | --- |
| LIQUID (mc_al) | $(\text{Al},\text{Cr},\text{Cu},\text{Fe},\text{Mg},\text{Mn},\text{Ni},\text{Sc},\text{Si},\text{Ti},\text{Zn},\text{Zr})_1$ | single sublattice; all elements of the database mix on it |
| Graphite (mc_fe) | $(\text{B},\text{C})_1$ | single sublattice; only B and C, regardless of the other elements in the system |
| BCC_A2 (mc_fe) | $(\text{Al},\text{Co},\text{Cr},\ldots,\text{W},\text{Y})_1(\text{B},\text{C},\text{H},\text{N},\text{O},\text{Va})_3$ | 2 sublattices; substitutional lattice sites and octahedral interstices, the latter partially vacant (vacancies as constituent) |
| BCC_B2 (mc_ni) | $(\text{Fe},\text{Ni},\text{Si},\text{Ti})_{0.5}(\text{Fe},\text{Ni},\text{Si},\text{Ti})_{0.5}(\text{Va})_3$ | 3 sublattices; two sublattices for the metal atoms allow modeling of chemical ordering; interstices occupied by vacancies only |
| GAMMA_PRIME (mc_ni) | $(\text{Al},\text{Co},\text{Cr},\ldots,\text{W},\text{Zr})_{0.25}(\text{Al},\text{Co},\text{Cr},\ldots,\text{W},\text{Zr})_{0.75}$ | 2 sublattices; ordered L1₂ structure, identical constituents on both sublattices |
| Cementite (mc_fe) | $(\text{Al},\text{Co},\text{Cr},\ldots,\text{V},\text{W})_3(\text{B},\text{C},\text{N})_1$ | 2 sublattices; metals and interstitials on separate sublattices |
| Sigma (mc_ni) | $(\text{Co},\text{Fe},\text{Mn},\text{Ni},\text{Ti})_8(\text{Cr},\text{Mo},\text{V},\text{W})_4(\text{Co},\text{Cr},\text{Fe},\text{Mn},\text{Mo},\text{Ni},\text{Si},\text{V},\text{W})_{18}$ | 3 sublattices; elements distributed among several sublattices |
| M23C6 (mc_ni) | $(\text{Co},\text{Cr},\text{Fe},\text{Mn},\text{Ni},\text{Ti},\text{V})_{20}(\text{Co},\text{Cr},\text{Fe},\text{Mn},\text{Mo},\text{Ni},\text{Ti},\text{V},\text{W})_3(\text{B},\text{C})_6$ | 3 sublattices; simplified description |
| M23C6_WY (mc_ni) | $(\text{Cr},\text{Mo})_1(\text{Cr},\text{Mo})_2(\text{Cr})_8(\text{Co},\text{Cr},\text{Ni},\text{Ti})_{12}(\text{B},\text{C})_6$ | 5 sublattices; description mapping all crystallographic (Wyckoff) sites |
| M7C3 (mc_fe) | $(\text{Co},\text{Cr},\text{Fe},\text{Mn},\text{Mo},\text{Ni},\text{Si},\text{V},\text{W})_7(\text{C})_3$ | 2 sublattices; single constituent on one sublattice |
| AlN (mc_fe) | $(\text{Al})_1(\text{N})_1$ | 2 sublattices; stoichiometric binary phase |
| Q_PHASE_H (mc_al) | $(\text{Al})_4(\text{Cu})_2(\text{Mg})_8(\text{Si})_7$ | 4 sublattices; stoichiometric quaternary phase |

The constituents of each sublattice are fixed by the phase description in the database. In a calculation, only those constituents are used which are among the elements selected for the system. Thus, depending on the selection of elements, a sublattice may contain fewer constituents than listed in the database, but never other ones.

The choice of the formula unit, and thus the scale of the site numbers $a_s$, is arbitrary. Common choices are integer site numbers of the crystallographic formula unit, e.g. 4 : 2 : 8 : 7 for the Q phase, site numbers which sum up to one, e.g. 0.667 : 0.333 for M2B, or site numbers which sum up to one on the substitutional sublattices only, with the interstitial sublattices scaled accordingly, e.g. 0.5 : 0.5 : 3 for BCC_B2 or 1 : 3 for BCC_A2. All these forms are equivalent, provided that the energy parameters in the database refer to the same formula unit, since only the ratio of the site numbers is relevant after normalization of the Gibbs energy to one mole of atoms (see [Normalization of the Gibbs energy](#normalization-of-the-gibbs-energy)).

Depending on the number of sublattices and constituents, the following cases can be distinguished:

- With a single sublattice, the model describes a substitutional solution, in which all constituents mix randomly. This is used, for instance, for the liquid phase. In this case, the composition of the phase is given directly by the fractions of the constituents on its single sublattice.
- With several sublattices, the model describes phases in which different kinds of atoms occupy different kinds of positions, or the same atoms occupy several kinds of positions with different preferences. This applies to crystalline phases, e.g. carbon occupies the octahedral interstices in the iron lattice, while the substitutional elements occupy the regular lattice sites. Ordered phases and compounds are described in the same way.
- If each sublattice is occupied by a single constituent, the phase is stoichiometric (e.g. AlN $(\text{Al})_1(\text{N})_1$ or the Q phase $(\text{Al})_4(\text{Cu})_2(\text{Mg})_8(\text{Si})_7$). All site fractions are then equal to one, the ideal and excess contributions vanish, and the Gibbs energy is given directly by the database parameter of the single end-member.
- A sublattice can also be occupied by a single constituent, while the other sublattices are mixed, e.g. $(\text{Co},\text{Cr},\ldots,\text{W})_7(\text{C})_3$ in M7C3 or $(\text{Cr},\text{Fe},\text{Mn},\text{Mo},\text{Ni})_{2/3}(\text{B})_{1/3}$ in M2B. Such phases are stoichiometric only with respect to that sublattice, and their composition can vary only through the mixed sublattices.

Note: The number of sublattices is a modeling choice and is not uniquely determined by the crystal structure. The same phase can be described with more or less crystallographic detail. For instance, the 23 metal positions of the M23C6 carbide are grouped into two sublattices (20 + 3 sites) in the phase M23C6, whereas the phase M23C6_WY distinguishes all four crystallographically different metal positions (1 + 2 + 8 + 12 sites, according to their Wyckoff positions). A more detailed description represents the site preferences of the elements more accurately, but the number of possible combinations of constituents, which must be described in the database, increases rapidly with the number of sublattices.


## Site fractions

The occupation of the sublattices is described by the site fractions $y_i^{(s)}$, i.e. the fraction of sites on sublattice $s$ which are occupied by constituent $i$. On every sublattice, the site fractions sum up to one:

\[
\sum_i y_i^{(s)} = 1
\]

Vacancies are treated as a regular constituent of a sublattice. The site fractions are the independent variables, in which the Gibbs energy of the phase is formulated (see [Gibbs energy of a phase – overview](gibbs_energy.md)). They are also the variables, which are adjusted in the equilibrium calculation.

## Number of atoms per formula unit

The Gibbs energy contributions are evaluated per formula unit of the phase. Since vacancies are not atoms, the number of atoms $N_\text{at}$ per formula unit is

\[
N_\text{at} = \sum_s a_s \left( 1 - y_\text{Va}^{(s)} \right)
\]

where $y_\text{Va}^{(s)}$ is zero for sublattices without vacancies. In the *MatCalc* code, $N_\text{at}$ is denoted as "site sum".

Important note: For phases with vacancies on a sublattice, $N_\text{at}$ is not a constant. It depends on the actual phase constitution. For instance, $N_\text{at}$ of BCC_A2 with the constitution given above varies between 1 (no interstitial atoms) and 4 (all octahedral interstices occupied).

## Mole fractions

The mole fraction $x_i$ of element $i$ in the phase is obtained from the site fractions as

\[
x_i = \frac{1}{N_\text{at}} \sum_s a_s \, y_i^{(s)}
\]

Each sublattice contributes with its number of sites. If an element occurs on several sublattices, its mole fraction is the sum of its contributions on all of them. The contribution of a single sublattice $s$, i.e. $a_s y_i^{(s)} / N_\text{at}$, is the mole fraction of element $i$ on sublattice $s$.

Example: For FCC_A1 in the Fe-C system, $(\text{Fe})_1(\text{C},\text{Va})_1$, the number of atoms is $N_\text{at} = 1 + y_\text{C}$, and the mole fraction of carbon is

\[
x_\text{C} = \frac{y_\text{C}}{1 + y_\text{C}} \qquad \Leftrightarrow \qquad y_\text{C} = \frac{x_\text{C}}{1 - x_\text{C}}
\]

For cementite in the Fe-C system, $(\text{Fe})_3(\text{C})_1$, $N_\text{at} = 4$ and $x_\text{C} = 0.25$.

## u-fractions

In phases with interstitial elements, it is often convenient to relate the composition to the substitutional elements only. The u-fraction of element $i$ is defined as

\[
u_i = \frac{x_i}{\sum_{j \in \text{subst}} x_j}
\]

where the sum runs over all substitutional elements of the phase. For the substitutional elements, the u-fractions are not affected by the amount of interstitial elements in the phase.

*MatCalc* classifies the elements of each phase as substitutional or interstitial based on the sublattices on which they occur:

- substitutional: the element occurs on a sublattice together with the matrix element of the system,
- interstitial: the element occurs on a sublattice which contains vacancies, or it has a molar mass between 11 and 18 g/mol (C, N, O), or it is C or N on a sublattice of a phase with more than one sublattice.

If both conditions apply, the element is treated as interstitial.

## Normalization of the Gibbs energy

All Gibbs energy contributions except of the constant offset (parameter `SE`) are evaluated per formula unit. The molar Gibbs energy of the phase, i.e. per mole of atoms, is obtained by division by $N_\text{at}$:

\[
G_m^\varphi = \frac{G_\text{f.u.}^\varphi}{N_\text{at}}
\]

Note: Since $N_\text{at}$ depends on the vacancy site fractions, the normalization factor changes with the content of interstitial elements. This has to be considered when the Gibbs energy of the phase is differentiated with respect to the site fractions, e.g. in the evaluation of chemical potentials.

## MatCalc variables

| Variable | Meaning |
| --- | --- |
| `Y$phase$element$sublattice` | site fraction $y_i^{(s)}$ of element on sublattice of phase |
| `YX$phase$element$sublattice` | mole fraction of element on sublattice of phase, $a_s y_i^{(s)}/N_\text{at}$ (zero for vacancies) |
| `X$phase$element` | mole fraction $x_i$ of element in phase |
| `U$phase$element` | u-fraction $u_i$ of element in phase |

The sublattices are numbered starting with 0, e.g. `Y$FCC_A1$C$1` is the site fraction of carbon on the interstitial sublattice of FCC_A1.
