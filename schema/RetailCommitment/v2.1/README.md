# RetailCommitment -- v2.1

**Schema Pack Version:** 2.1.0
**Status:** Initial release

## Notes

Initial release of the RetailCommitment schema targeting the v2.1 generalised model.

## Beckn Integration Notes

- **Core Schema References**: Uses $ref to PriceSpecification from `../../v2.0.0/beckn.yaml`
- **Context/Vocab**: Imports generalized context and vocab from Beckn core v2
- **Protocol Version**: Targets Beckn protocol v2.0 with v2.1 semantic extensions
- **Backward Compatibility**: Not directly compatible with v2 (which didn't have this schema); however, v2 systems can continue using domain-specific fields as fallback


## Migration Guide (v2 → v2.1)

For systems migrating from v2 to v2.1:


### Coexistence Strategy
- v2.1 recommends using commitmentAttributes
- v2 systems continue using resource-specific fields
- API implementations should accept both during transition period (v2.1-v2.2)


## Version History

- **v2.1.0** (Current) – Initial release; NEW schema consolidating commitment details from v2 domain-specific models
- **Not in v2.0.0** – Fields were scattered across FoodAndBeverageItem, HomeAndKitchenItem, etc.
