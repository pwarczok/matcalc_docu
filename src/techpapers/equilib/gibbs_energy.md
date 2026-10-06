# TechPaper #2026001: Gibbs energy of a phase – overview

## Compatibility

<!-- TODO: confirm first version this description applies to -->
MatCalc version: 6.00 - …  
Author: P. Warczok  
Created: 2026-10-05  
Revisions:

## Objectives

This paper gives an overview of how the molar Gibbs energy of a phase is evaluated in *MatCalc* from the parameters of the thermodynamic database (`*.tdb`). It summarizes the individual contributions and serves as the entry point to a series of papers, in which each contribution is described in detail.

## Related documents

1. [Sublattice model, site fractions and normalization](sublattice_model.md)
2. [Equilibrium vacancy concentration](equilib_vac.md)
3. [Equilibrium trapping](trapping.md)
4. [The multi-component transient nucleation rate](../nucleation/nucleation_rate.md)

# Main document

*MatCalc* follows the CALPHAD approach: every phase is described by its own Gibbs energy function, which depends on temperature and on the constitution of the phase. The parameters of these functions are stored in the thermodynamic database. The molar Gibbs energy of the phases is the most basic quantity evaluated by *MatCalc* – practically every other result is derived from it:

- equilibrium phase fractions and phase compositions, obtained by minimization of the total Gibbs energy of the system,
- chemical potentials of the elements,
- chemical driving forces for phase transformations and for nucleation of precipitates,
- thermodynamic quantities derived from the Gibbs energy, such as enthalpy, entropy and heat capacity.

## Constitution of a phase

The phases are described with the sublattice model (compound energy formalism). The constitution of a phase is given by the site fractions $y_i^{(s)}$ of all constituents $i$ on all sublattices $s$, each sublattice having $a_s$ sites per formula unit. The energy contributions listed below are evaluated per formula unit and are finally normalized to one mole of atoms. This is described in detail in [Sublattice model, site fractions and normalization](sublattice_model.md).

## Contributions to the Gibbs energy

The molar Gibbs energy of a phase $\varphi$ is evaluated as

\[
G_m^\varphi = \frac{1}{N_\text{at}} \left( {}^\text{ref}G + {}^\text{id}G + {}^\text{ex}G + {}^\text{mag}G \right) + G_\text{SE}
\]

with

$N_\text{at}$ - number of atoms (sites not occupied by vacancies) per formula unit, see [Sublattice model, site fractions and normalization](sublattice_model.md)

${}^\text{ref}G$ - reference term (surface of reference)

${}^\text{id}G$ - ideal configurational term

${}^\text{ex}G$ - excess term

${}^\text{mag}G$ - magnetic term

$G_\text{SE}$ - constant offset (parameter `SE`)

The individual contributions are summarized below.

### Reference term

The reference term is the weighted sum of the Gibbs energies of all "end-members" of the phase, i.e. of all combinations with exactly one constituent per sublattice:

\[
{}^\text{ref}G = \sum_{I} \left( \prod_{s} y_{I_s}^{(s)} \right) {}^\circ G_I
\]

The Gibbs energies ${}^\circ G_I$ of the end-members are given by the `G` parameters of the database, e.g. `G(FCC_A1,FE:VA)`.

### Ideal configurational term

The ideal configurational term accounts for the random mixing of constituents within each sublattice:

\[
{}^\text{id}G = RT \sum_{s} a_s \sum_{i} y_i^{(s)} \ln y_i^{(s)}
\]

When trapping is active, this term is modified to account for the occupation of trap positions, see [Equilibrium trapping](trapping.md).

### Excess term

The excess term describes the deviation from ideal mixing. It is composed of interaction parameters (`L` parameters in the database), multiplied with the site fractions of the interacting constituents. Binary interactions are expanded in Redlich-Kister polynomials:

\[
{}^\text{ex}G = \sum_{s} \sum_{i<j} y_i^{(s)} y_j^{(s)} \cdots \sum_{\nu} {}^{\nu}L_{i,j} \left( y_i^{(s)} - y_j^{(s)} \right)^{\nu} + \ldots
\]

where "$\cdots$" stands for the site fractions of the constituents on the remaining sublattices and $\nu$ is the order of the interaction parameter. Ternary interaction parameters are treated accordingly.

### Magnetic term

For ferro- and antiferromagnetic phases, the magnetic contribution according to Inden, Hillert and Jarl is added:

\[
{}^\text{mag}G = RT \ln(\beta + 1) \, f(\tau), \qquad \tau = \frac{T}{T_C}
\]

The Curie (or Néel) temperature $T_C$ and the mean magnetic moment $\beta$ depend on the constitution of the phase and are defined by the `TC` and `BMAGN` parameters of the database. The function $f(\tau)$ depends on the structure-dependent factor $p$, which is defined with the `TYPE_DEFINITION` keyword in the database.

### Constant offset

A constant energy offset can be defined with the `SE` parameter. Unlike all other contributions, it is related directly to one mole of atoms.

## Ordered phases

Ordered phases, for which a disordered contribution is attached in the database (keyword `ATTACH_CONTRIBUTION`), are evaluated as

\[
G_m = G_m^\text{dis}(x) + G_m^\text{ord}(y) - G_m^\text{ord}(y = x)
\]

where $G_m^\text{dis}$ is evaluated with the mole fractions $x$ and $G_m^\text{ord}$ with the site fractions $y$ of the ordered phase. The last term cancels the contribution of the ordered part when the phase is disordered, i.e. when the site fractions on all ordered sublattices are equal.

## Database parameters not entering the Gibbs energy

Some parameters of the thermodynamic database are stored with the phase, but they do not contribute to its Gibbs energy:

| Parameter | Meaning | Use in *MatCalc* |
| --- | --- | --- |
| `HMVA`, `SMVA` | enthalpy and entropy of vacancy formation | equilibrium vacancy concentration, see [Equilibrium vacancy concentration](equilib_vac.md) |
| `IS` | (internal stress) | read, but currently not used |

Diffusion data (mobilities) and physical data (e.g. density) are read from separate databases (`*.ddb`, `*.pdb`) and do not affect the Gibbs energy.

## Use of the Gibbs energy in subsequent calculations

Together with the Gibbs energy, *MatCalc* evaluates its first and second derivatives with respect to all site fractions and temperature. These are used

- in the minimization of the total Gibbs energy of the system (equilibrium calculations),
- for the evaluation of the chemical potentials of the elements,
- for the evaluation of the chemical driving force of phases, which enters, for instance, the nucleation model (see [The multi-component transient nucleation rate](../nucleation/nucleation_rate.md)).

The total molar Gibbs energy of the system is the sum of the molar Gibbs energies of all phases, weighted with their phase fractions $f^\varphi$:

\[
G_m = \sum_{\varphi} f^\varphi G_m^\varphi
\]

## MatCalc variables

The following variables (category "state variables") give access to the quantities described above:

| Variable | Meaning | Unit |
| --- | --- | --- |
| `GMP$phase` | molar Gibbs energy of phase | J/mol |
| `GM` | molar Gibbs energy of system | J/mol |
| `HMP$phase` | molar enthalpy of phase | J/mol |
| `SMP$phase` | molar entropy of phase | J/(mol K) |
| `SCMP$phase` | molar configurational entropy of phase | J/(mol K) |
| `MHM$phase` | molar magnetic enthalpy of phase | J/mol |
| `TC$phase` | Curie temperature of phase | K |

"Molar" refers to one mole of atoms. Appending the flag `$k` or `$v` to the variable name (e.g. `GMP$FCC_A1$k`) converts the value to J/kg or J/m³, respectively.
