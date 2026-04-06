# RetailPerformance -- v2.1

**Schema Pack Version:** 2.1.0
**Status:** Initial release

## Notes

Initial release of the RetailPerformance schema targeting the v2.1 generalised model.

## Beckn Integration Notes

- **Core Schema References**: Uses `$ref` to Address and PriceSpecification from `../../v2.0.0/beckn.yaml`
- **Context/Vocab**: Imports generalized context and vocab from Beckn core v2
- **Protocol Version**: Targets Beckn protocol v2.0 with v2.1 semantic extensions
- **Backward Compatibility**: Not directly compatible with v2 fulfillmentAttributes; explicit migration mapping required


## Version History

- **v2.1.0** (Current) – Initial release; migration from fulfillmentAttributes with installationScheduling addition
- **v2.0.0** – Original fulfillmentAttributes (deprecated in favor of performanceAttributes)
