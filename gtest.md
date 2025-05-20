# gtests

## Test data write

- ReactionTest.write data

## ReactionTest fixture
- compare_sources()
  - [ ] read test input data
    - [ ] Amjuel:
      - [ ] electron["density"], electron["temperature"],from_ion["density"] SET with gradients (determines rate)
      - [ ] from_ion["charge"], to_ion["charge"] SET to the same thing (required for charge cons check, both ignored otherwise)
      - [ ] from_ion["temperature"] SET (affects from_ion["energy_source"], to_ion["energy_source"])
      - [ ] to_ion["velocity"] SET (affects to_ion["energy_source"])
      - [ ] electron["velocity"], electron["AA"] SET to constants (needed for electron["energy_source"])
      - [ ] from_ion["AA"], from_ion["velocity"] SET (affects from_ion["momentum_source"], to_ion["momentum_source"], to_ion["energy_source"])
      - [ ] to_ion["AA"] SET to from_ion["AA"] (required for mass cons check)
    - Tests
      - Values of
        - [ ] from_ion["density_source"]
        - [ ] to_ion["density_source"]
        - [ ] from_ion["momentum_source"]
        - [ ] to_ion["momentum_source"]
        - [ ] electron["density_source"]
        - [ ] from_ion["energy_source"]
        - [ ] to_ion["energy_source"]
        - [ ] electron["energy_source"]
- [ ] read test data for the reaction (including species; nT_test_matrix)
- [x] component as member var, construct via init list using static Options obj
- foreach (n, T) in nT_test_matrix: 
  - foreach sp
    - foreach source_type
    - ASSERT_TRUE(IsFieldEqual(get<Field3D>(state["species"][sp][source_type]), val_from_data, "RGN_NOBNDRY"));



## Existing tests for reactions

- HydrogenCXTest (tests/unit/test_hydrogen_charge_exchange.cxx)
  - .CreateComponent: Just creates a HydrogenChargeExchangeIsotope component, and presumably throws if it fails? Don't think the exception is caught
  - .RateAt1eV
    - Creates a HydrogenChargeExchangeIsotope component
    - Creates a state object with species h and h+, with T_h=0, T_h+=1
    - Calls transform()
    - ASSERTS that a momentum_source and energy_source exist for both species
    - ASSERTS that the h energy source Field3D has a particule value using IsFieldEqual from test_extras.hxx
- HydrogenRCTest (tests/unit/test_amjuel_hyd_recombination.cxx)
  - .CreateComponent: Just creates a AmjuelHydRecombinationIsotope component, and presumably throws if it fails? Don't think the exception is caught
  - .DensitySourceSigns
    - Creates a AmjuelHydRecombinationIsotope component
    - Creates a state object with species e, h and h+, with n_e=n_T=1, n_h=T_h=v_h=1, n_h+=T_h+v_h+=1
    - Calls transform()
    - ASSERTS that an energy_source exist for "e"
    - Loops over the common region, asserting that
      - The h and h_ sources have the right sign
      - The e dens source and h+ dens source have the same value

Both existing tests use FakeMeshFixture, which 
- Creates a global MpiWrapper
- Creates two global meshes (FakeMesh) of size 3x5x7
  - mesh
  - staggered_mesh  
- Creates a global mesh (FakeMesh) of size 3x5x7
- Creates a second global mesh (FakeMesh) of size 3x5x7
