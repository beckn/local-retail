# RetailContract -- v2.1

**Schema Pack Version:** 2.1.0
**Status:** Initial release

## Notes

Initial release of the RetailContract schema targeting the v2.1 generalised model.

## Beckn Integration Notes

- **Core Schema References**: Uses $ref to PriceSpecification from `../../v2.0.0/beckn.yaml`
- **Context/Vocab**: Imports generalized context and vocab from Beckn core v2
- **Protocol Version**: Targets Beckn protocol v2.0 with v2.1 semantic extensions
- **Backward Compatibility**: Direct 1:1 mapping from v2 orderAttributes enables straightforward migration


## Version History

- **v2.1.0** (Current) – Initial release; direct migration from RetailCoreOrderAttributes with no structural changes
- **v2.0.0** – Original orderAttributes in Beckn retail spec (deprecated in favor of contractAttributes naming)
