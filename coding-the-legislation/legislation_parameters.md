# Implementing legislation parameters

[Legislation parameters](../parameters.md) can be found in the `parameters` directory of your country package.

The parameters are organized with in a [tree structure](https://en.wikipedia.org/wiki/Tree_structure).

>**Example**: `tax_on_salary.public_sector.rate` can be found in `parameters/tax_on_salary/public_sector/rate.yml`.

Example of a `parameters` directory:
* parameters
  * tax_on_salary
    * **tax_scale.yaml**
    * public_sector
      * **rate.yaml**
  * universal_income
    * **minimum_age.yaml**
    * **amount.yaml**


In this file structure:
 - `tax_on_salaries`, `tax_on_salary.public_sector`, `universal_income` are **nodes**;
 - `tax_on_salaries.tax_scale`, `tax_on_salary.public_sector.rate`,`universal_income.minimum_age`,`universal_income.amount` are **parameters** (or scales).

## How to write a new parameter
> if you wish to update a parameter, read our [legislation evolution page](./40_legislation_evolutions.md).

1. Find where the parameter fits

A parameter is located inside a **node**, that has the same name as the directory it is contained in.
>**Example**: `tax_on_salary.public_sector` is the node that contains the `tax_on_salary.public_sector.rate`parameter.

2. Create a new parameter YAML file

A legislative parameter is defined by a YAML file of the same name. Possible attributes are:
* `description` (optional) Description;
* `reference` (optional) Legislative reference;
* `unit` (optional) Can be:
  - `year` : The values are years;
  - `currency`: The values are in the unit of currency of the country;
  - `/1`: The values are percentages, with `1.0`=100%;
* `values`: Value of the parameter for several dates.

Sample file `parameters/universal_income/amount.yaml`
```yaml
description: Universal income
unit: currency
values:
  1993-01-01:
    value: 1000
  2010-01-01:
    value: 1500
    reference: http://law.reference.org/universal_income
  2020-01-01:
    expected: 1700
```

In this example, the parameter `universal_income.amount` is:
* undefined before 1993;
* equal to 1000 from 1993 to 2010;
* equal to 1500 in 2010
* expected to be raised to 1700 "local currency" in 2020.

The ordering of the dates has no effect. It is recommended to add legislative references for every value?

3. Use the parameter in a variable

See [this example of a variable using legislation parameters](./10_basic_example.md#example-with-legislation-parameters).

### Naming conventions and reserved words

Names should begin with a lowercase letter and should contain only lowercase letters and the underscore (`_`).

The following keywords are reserved and should not be used as names : `description`, `reference`, `values`, `brackets`.

YAML parameter files should not be name `index.yaml`.

### Advanced uses

#### Use a YAML files to define nodes

A node can be defined with a YAML file instead of a directory. In such a case, the name of the file defines the name of the node. Such a file can define children nodes (which can define grandchildren...).

Sample `parameters/tax_on_salary.yaml`:

```yaml
description: Tax on salaries
reference: http://fiscaladministration.government/tax_on_salaries.html
tax_scale:
  bracket:
    ...
public_sector:
  description: Tax on salaries for public sector
  rate:
    values:
     ...
```

#### Create Scales

Scales are constituted of brackets. Brackets are defined by amounts, bases, rates, average rates and thresholds.

Sample `parameters/tax_on_salary/tax_scale.yaml`:
```yaml
description: Scale for tax on salaries
brackets:
- rate:
    1950-01-01:
      value: 0.0
    2010-01-01:
      value: 0.02
  threshold:
    1950-01-01:
      value: 0.0
- rate:
    1950-01-01:
      value: 0.2
  threshold:
    1950-01-01:
      value: 2000
```

Example: [the french tax scale on salaries](https://legislation.openfisca.fr/parameters/impot_revenu.bareme)

#### Import parameters from IPP tables

> This section applies only to OpenFisca-France.

The [<abbr title="Institut des politiques publiques">IPP</abbr>](http://www.ipp.eu/) is a French centre in economics which produces tax and benefit tables in the `XLSX` format, with parameters history.

The OpenFisca team works on importing those data into the YAML parameter files of OpenFisca-France.

See [this README](https://github.com/openfisca/openfisca-france/tree/master/openfisca_france/scripts/parameters/baremes_ipp) for more information.
