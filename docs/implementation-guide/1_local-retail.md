# 1. Implementation Guide

This document contains the REQUIRED and RECOMMENDED standard functionality that must be implemented by any retail Provider Platform a.k.a Seller app a.k.a BPPs and retail Consumer Platform a.k.a Buyer App a.k.a BAPs.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 [RFC2119].

## 1.1 Discovery of Retail Catalog

### 1.1.1 Recommendations for BPPs
The following recommendations need to be considered when implementing discovery functionality for a retail BPP

- REQUIRED. The BPP MUST implement the `search` endpoint to receive an `Intent` object sent by BAPs  
- REQUIRED. The BPP MUST return a catalog of retail products on the `on_search` callback endpoint specified in the `context.bap_uri` field of the `search` request body.
- REQUIRED. The BPP MUST map its retail catalog to the `Item` schema.
- REQUIRED. Any retail seller(store) related information like name, logo, short description must be mapped to the `Provider.descriptor` schema
- REQUIRED. Any form that must be filled before receiving a quotation must be mapped to the `XInput` schema
- REQUIRED. If the platform wants to group its products under a specific category, it must map each category to the `Category` schema
- REQUIRED. Any order delivery related information MUST be mapped to the `Fulfillment` schema.
- REQUIRED. If the BPP does not want to respond to a search request, it MUST return a `ack.status` value equal to `NACK`
- RECOMMENDED. Upon receiving a `search` request, the BPP SHOULD return a catalog that best matches the intent. This can be done by indexing the catalog against the various probable paths in the `Intent` schema relevant to typical retail use cases

### 1.1.2 Recommendations for BAPs
- REQUIRED. The BAP MUST call the `search` endpoint of the BG to discover multiple BPPs on a network
- REQUIRED. The BAP MUST implement the `on_search` endpoint to consume the `Catalog` objects containing retail Products sent by BPPs.
- REQUIRED. The BAP MUST expect multiple catalogs sent by the respective retail Providers on the network
- REQUIRED. The retail products can be found in the `Catalog.providers[].items[]` array in the `on_search` request
- REQUIRED. If the `catalog.providers[].items[].xinput` object is present, then the BAP MUST redirect the user to, or natively render the form present on the link specified on the `items[].xinput.form.url` field.
- REQUIRED. If the `catalog.providers[].items[].xinput.required` field is set to `"true"` , then the BAP MUST NOT fire a `select`, `init` or `confirm` call until the form is submitted and a successful response is received
- RECOMMENDED. If the `catalog.providers[].items[].xinput.required` field is set to `"false"` , then the BAP SHOULD allow the user to skip filling the form

### Discovery Flow
1. Buyer log-in to the Buyer App and make a search request by product name, category, provider, or location.
2. Buyer Apps translate the buyer search into /search request and hit the Gateway Endpoint of search
3. Gateway broadcast the search request to different seller apps.
4. Seller App translate the search intent and send the filtered catalogue to the buyer app using /on_search request.
5. Buyer App render the result in the UI, so that it become visible to the buyers.

### Discovery Example
A search request for a local retail  may look like this
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "search",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "message_id": "6104c0a3-d1d1-4ded-aaa4-76e4caf727ce",
    "timestamp": "2023-11-06T09:41:09.673Z"
  },
  "message": {
    "intent": {
      "category": {
        "descriptor": {
          "code": "electronics"
        }
      },
      "item": {
        "descriptor": {
          "name": "earphone"
        }
      },
      "fulfillment": {
        "type": "Delivery",
        "stops": [
          {
            "location": {
              "gps": "28.4594965,77.0266383"
            }
          }
        ]
      }
    }
  }
}
```

An example catalog of a local retail may look like this
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_search",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "6104c0a3-d1d1-4ded-aaa4-76e4caf727ce",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T09:41:09.708Z",
    "ttl": "PT10M"
  },
  "message": {
    "catalog": {
      "descriptor": {
        "name": "HBO"
      },
      "providers": [
        {
          "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider",
          "descriptor": {
            "name": "Venky.Mahadevan@Bazaar"
          },
          "locations": [
            {
              "id": "./retail.kirana/ind.blr/1@tourism-bpp-infra2.becknprotocol.io.provider_location",
              "gps": "12.909955,77.596316"
            }
          ],
          "categories": [
            {
              "id": "c1",
              "descriptor": {
                "code": "grocery",
                "name": "grocery"
              }
            },
            {
              "id": "c2",
              "descriptor": {
                "code": "electronics",
                "name": "electronics"
              }
            }
          ],
          "fulfillments": [
            {
              "id": "f1",
              "type": "Delivery"
            },
            {
              "id": "f2",
              "type": "Self-Pickup"
            }
          ],
          "items": [
            {
              "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
              "descriptor": {
                "images": [
                  {
                    "url": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
                  }
                ],
                "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow - Water bottle",
                "short_desc": "InstaCuppa Stainless Steel Thermos Flask Water Bottle with Sports Sipper Lid, Double Walled Vacuum Insulation",
                "long_desc": "<div> <ul> <li>ULTRA MODERN DESIGN - Our thermos bottle is crafted with a unique and modern design. Gone are the days of old and boring flasks. Guaranteed to impress your colleagues, friends & family.</li> <li>ADVANCED TEMPERATURE CONTROL – A double-wall, vacuum-insulated design helps lock in heat for up to 12 hours and cold for up to 24!</li> <li>ELIMINATES CONDENSATION – Offering improved grip and control, these innovative dual-layer bottles offer a slip-resistant surface that’s free of sweat and condensation..</li> <li>LEAK-PROOF and ECO-FRIENDLY – Remove, and clean, the large, screw on lid provides faster access to water inside and won’t spill a drop even when it’s tipped upside or put in your gym bag.</li> <li>The distress quilted jacket is a versatile fashion choice you can wear on any occasion. A style essential piece for Women which will reveal your strong sense of personality</li> </ul> <div> <p><b>Product Details</b></p> <ul> <li>Advanced Temperature Retention.This thermos water bottle ensures your beverages will remain hot or cold for a long time.Hot for up to 12 hours.Cold for up to 24 hours.</li> <li>Retains Original Flavors.Vacuum insulation ensures this travel thermos water bottle is airtight and retains the original flavor of your beverages.Also, this bottle is B.P.A Free.</li> <li>Premium Quality Materials.This stylish bottle is a double-walled vacuum insulated and made from premium 304-grade stainless steel - which makes this flask bottle.</li> </ul> </div>"
              },
              "matched": true,
              "price": {
                "listed_value": "1200.0",
                "currency": "INR",
                "value": "1200.0"
              },
              "recommended": true,
              "location_id": "./retail.kirana/ind.blr/1@tourism-bpp-infra2.becknprotocol.io.provider_location",
              "category_id": "c1",
              "fulfillment_id": "f1",
              "tags": [
                {
                  "descriptor": {
                    "name": "item-cataegory"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "category"
                      },
                      "value": "retail"
                    }
                  ]
                },
                {
                  "descriptor": {
                    "name": "item-properties"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "waterbottle"
                      },
                      "value": "y"
                    },
                    {
                      "descriptor": {
                        "name": "Trekking"
                      },
                      "value": "y"
                    },
                    {
                      "descriptor": {
                        "name": "Sipper"
                      },
                      "value": "y"
                    },
                    {
                      "descriptor": {
                        "name": "Hiking"
                      },
                      "value": "y"
                    }
                  ]
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

## 1.2 Initiating Purchase for Retail Product
This section provides recommendations for implementing the APIs related to a purchasing retail product.

### 1.2.1 Recommendations for BPPs

#### 1.2.1.1 Selecting a retail product from the catalog
- REQUIRED. The BPP MUST implement the `select` endpoint on the url specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- OPTIONAL. The BPP MUST check for a form submission at the URL specified on the `xinput.form.url` before acknowledging a `select` request.
- REQUIRED. If the retail provider(store) has successfully received the information submitted by the buyer, the BPP must return an acknowledgement with `ack.status` set to `ACK` in response to the `select` request
- REQUIRED. If the retail provider(store) has returned a successful acknowledgement to a `select` request, it MUST send the offer encapsulated in an `Order` object

#### 1.2.1.2 Initializing a retail order
- REQUIRED. The BPP MUST implement the `init` endpoint on the url specified in  the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.2.1.3 Confirming the retail order
- REQUIRED. The BPP MUST implement the `confirm` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.2.2 Recommendations for BAPs

#### 1.2.1.1 Selecting a retail product from the catalog
- OPTIONAL. The BAP user MUST submit the form on the url received from  `on_search`  under `xinput.form.url`.
- REQUIRED. The BAP MUST implement the `on_select` endpoint on the url specified in the `context.bap_uri` field sent during `select`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.2.2.2  Initializing a retail order
- REQUIRED. The BAP MUST hit the `init` endpoint on the url specified in  the `context.bpp_uri` field sent during `on_search`. 
- REQUIRED. The BAP MUST implement the `on_init` endpoint on the url specified in  the `context.bap_uri` field sent during `init`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.2.2.3 Confirming the retail order
- REQUIRED. The BAP MUST hit the `confirm` endpoint on the url specified in  the `context.bpp_uri` field sent during `on_search`. 
- REQUIRED. The BAP MUST implement the `on_confirm` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `confirm`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.2.3 Example Workflow
1. Buyer select a product from the catalog rendered from the search results.
2. Buyer clicks on "Add to Cart".
3. Buyer App send /select call to the seller app with product id and quantity and buyer's end location.
4. Seller app return the quotation in /on_select.
5. Buyer App render the quotation in the cart page.
6. Buyer select his delivery and shipping address.
7. Buyer app send /init call to the seller app with the buyer's billing and shipping detail.
8. Seller App return fulfilment and payment terms along with the quotation.
9. Buyer App render the details in the checkout page.
10. Buyer Complete the payment.
11. Buyer app send /confirm call to the seller app with the payment details and order confirmation.
12. Seller app return the confirmed order with order id, using /on_confirm call.

### 1.2.4 Example Requests

Below is an example of a `select` request
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "select",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "6d098f3a-4873-4b2e-935e-e4d6be92eb01",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T09:44:47.217Z"
  },
  "message": {
    "order": {
      "provider": {
        "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider"
      },
      "items": [
        {
          "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
          "selected": {
            "quantity": {
              "count": 2
            }
          }
        }
      ],
      "fulfillments": [
        {
          "id": "f1"
        }
      ]
    }
  }
}
```

Below is an example of an `on_select` callback
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_select",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "6d098f3a-4873-4b2e-935e-e4d6be92eb01",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T09:44:47.229Z",
    "ttl": "PT10M"
  },
  "message": {
    "order": {
      "provider": {
        "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider",
        "descriptor": {
          "name": "Venky.Mahadevan@Bazaar"
        },
        "locations": [
          {
            "id": "./retail.kirana/ind.blr/1@tourism-bpp-infra2.becknprotocol.io.provider_location"
          }
        ]
      },
      "items": [
        {
          "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
          "descriptor": {
            "images": [
              {
                "url": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
              }
            ],
            "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow - Water bottle",
            "short_desc": "InstaCuppa Stainless Steel Thermos Flask Water Bottle with Sports Sipper Lid, Double Walled Vacuum Insulation",
            "long_desc": "<div> <ul> <li>ULTRA MODERN DESIGN - Our thermos bottle is crafted with a unique and modern design. Gone are the days of old and boring flasks. Guaranteed to impress your colleagues, friends & family.</li> <li>ADVANCED TEMPERATURE CONTROL – A double-wall, vacuum-insulated design helps lock in heat for up to 12 hours and cold for up to 24!</li> <li>ELIMINATES CONDENSATION – Offering improved grip and control, these innovative dual-layer bottles offer a slip-resistant surface that’s free of sweat and condensation..</li> <li>LEAK-PROOF and ECO-FRIENDLY – Remove, and clean, the large, screw on lid provides faster access to water inside and won’t spill a drop even when it’s tipped upside or put in your gym bag.</li> <li>The distress quilted jacket is a versatile fashion choice you can wear on any occasion. A style essential piece for Women which will reveal your strong sense of personality</li> </ul> <div> <p><b>Product Details</b></p> <ul> <li>Advanced Temperature Retention.This thermos water bottle ensures your beverages will remain hot or cold for a long time.Hot for up to 12 hours.Cold for up to 24 hours.</li> <li>Retains Original Flavors.Vacuum insulation ensures this travel thermos water bottle is airtight and retains the original flavor of your beverages.Also, this bottle is B.P.A Free.</li> <li>Premium Quality Materials.This stylish bottle is a double-walled vacuum insulated and made from premium 304-grade stainless steel - which makes this flask bottle.</li> </ul> </div>"
          },
          "category_ids": [
            "c1"
          ],
          "price": {
            "listed_value": "1200.0",
            "currency": "INR",
            "value": "1200.0"
          }
        }
      ],
      "fulfillments": [
        {
          "id": "f1"
        }
      ],
      "quote": {
        "price": {
          "currency": "INR",
          "value": "1500.0"
        },
        "breakup": [
          {
            "title": "base-price",
            "price": {
              "currency": "INR",
              "value": "1200.0"
            }
          },
          {
            "title": "taxes",
            "price": {
              "currency": "INR",
              "value": "300.0"
            }
          }
        ]
      }
    }
  }
}
```


Below is an example of a `init` request
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "init",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "message_id": "06f49c01-65b7-4754-8b37-ab4e33688154",
    "timestamp": "2023-11-06T09:45:40.407Z"
  },
  "message": {
    "order": {
      "provider": {
        "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider"
      },
      "items": [
        {
          "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
          "selected": {
            "quantity": {
              "count": 2
            }
          }
        }
      ],
      "fulfillments": [
        {
          "type": "Delivery",
          "stops": [
            {
              "location": {
                "gps": "13.2008459,77.708736",
                "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
                "city": {
                  "name": "Gangamuthanahalli"
                },
                "state": {
                  "name": "Karnataka"
                },
                "country": {
                  "code": "IND"
                },
                "area_code": "75001"
              },
              "contact": {
                "phone": "919246394908",
                "email": "nc.rehman@gmail.com"
              }
            }
          ],
          "customer": {
            "person": {
              "name": "Motiur Rehman"
            },
            "contact": {
              "phone": "919122343344"
            }
          }
        }
      ],
      "billing": {
        "name": "Motiur Rehman",
        "phone": "9191223433",
        "email": "nc.rehman@gmail.com",
        "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
        "city": {
          "name": "Gangamuthanahalli"
        },
        "state": {
          "name": "Karnataka"
        }
      }
    }
  }
}
```

Below is an example of an `on_init` callback
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_init",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "06f49c01-65b7-4754-8b37-ab4e33688154",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T09:45:40.421Z",
    "ttl": "PT10M"
  },
  "message": {
    "order": {
      "provider": {
        "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider",
        "descriptor": {
          "name": "Venky.Mahadevan@Bazaar"
        },
        "locations": [
          {
            "id": "./retail.kirana/ind.blr/1@tourism-bpp-infra2.becknprotocol.io.provider_location"
          }
        ]
      },
      "items": [
        {
          "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
          "descriptor": {
            "images": [
              {
                "url": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
              }
            ],
            "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow - Water bottle",
            "short_desc": "InstaCuppa Stainless Steel Thermos Flask Water Bottle with Sports Sipper Lid, Double Walled Vacuum Insulation",
            "long_desc": "<div> <ul> <li>ULTRA MODERN DESIGN - Our thermos bottle is crafted with a unique and modern design. Gone are the days of old and boring flasks. Guaranteed to impress your colleagues, friends & family.</li> <li>ADVANCED TEMPERATURE CONTROL – A double-wall, vacuum-insulated design helps lock in heat for up to 12 hours and cold for up to 24!</li> <li>ELIMINATES CONDENSATION – Offering improved grip and control, these innovative dual-layer bottles offer a slip-resistant surface that’s free of sweat and condensation..</li> <li>LEAK-PROOF and ECO-FRIENDLY – Remove, and clean, the large, screw on lid provides faster access to water inside and won’t spill a drop even when it’s tipped upside or put in your gym bag.</li> <li>The distress quilted jacket is a versatile fashion choice you can wear on any occasion. A style essential piece for Women which will reveal your strong sense of personality</li> </ul> <div> <p><b>Product Details</b></p> <ul> <li>Advanced Temperature Retention.This thermos water bottle ensures your beverages will remain hot or cold for a long time.Hot for up to 12 hours.Cold for up to 24 hours.</li> <li>Retains Original Flavors.Vacuum insulation ensures this travel thermos water bottle is airtight and retains the original flavor of your beverages.Also, this bottle is B.P.A Free.</li> <li>Premium Quality Materials.This stylish bottle is a double-walled vacuum insulated and made from premium 304-grade stainless steel - which makes this flask bottle.</li> </ul> </div>"
          },
          "category_ids": [
            "c1"
          ],
          "price": {
            "listed_value": "1200.0",
            "currency": "INR",
            "value": "1200.0"
          }
        }
      ],
      "fulfillments": [
        {
          "type": "Delivery",
          "stops": [
            {
              "location": {
                "gps": "13.2008459,77.708736",
                "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
                "city": {
                  "name": "Gangamuthanahalli"
                },
                "state": {
                  "name": "Karnataka"
                },
                "country": {
                  "code": "IND"
                },
                "area_code": "75001"
              },
              "contact": {
                "phone": "919246394908",
                "email": "nc.rehman@gmail.com"
              }
            }
          ],
          "customer": {
            "person": {
              "name": "Motiur Rehman"
            },
            "contact": {
              "phone": "919122343344"
            }
          }
        }
      ],
      "quote": {
        "price": {
          "currency": "INR",
          "value": "1500.0"
        },
        "breakup": [
          {
            "title": "base-price",
            "price": {
              "currency": "INR",
              "value": "1200.0"
            }
          },
          {
            "title": "taxes",
            "price": {
              "currency": "INR",
              "value": "300.0"
            }
          }
        ]
      },
      "billing": {
        "name": "Motiur Rehman",
        "phone": "9191223433",
        "email": "nc.rehman@gmail.com",
        "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
        "city": {
          "name": "Gangamuthanahalli"
        },
        "state": {
          "name": "Karnataka"
        }
      },
      "payments": [
        {
          "status": "NOT-PAID",
          "type": "PRE-FULFILLMENT",
          "params": {
            "amount": "1500",
            "currency": "INR",
            "bank_code": "INB0004321",
            "bank_account_number": "1234002341"
          }
        }
      ]
    }
  }
}
```

Below is an example of a `confirm` request
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "confirm",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "58d0a142-68e3-48d9-92e8-fbc791e44949",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.280Z"
  },
  "message": {
    "order": {
      "provider": {
        "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider"
      },
      "items": [
        {
          "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
          "selected": {
            "quantity": {
              "count": 2
            }
          }
        }
      ],
      "fulfillments": [
        {
          "type": "Delivery",
          "stops": [
            {
              "location": {
                "gps": "13.2008459,77.708736",
                "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
                "city": {
                  "name": "Gangamuthanahalli"
                },
                "state": {
                  "name": "Karnataka"
                },
                "country": {
                  "code": "IND"
                },
                "area_code": "75001"
              },
              "contact": {
                "phone": "919246394908",
                "email": "nc.rehman@gmail.com"
              }
            }
          ],
          "customer": {
            "person": {
              "name": "Motiur Rehman"
            },
            "contact": {
              "phone": "919122343344"
            }
          }
        }
      ],
      "billing": {
        "name": "Motiur Rehman",
        "phone": "9191223433",
        "email": "nc.rehman@gmail.com",
        "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
        "city": {
          "name": "Gangamuthanahalli"
        },
        "state": {
          "name": "Karnataka"
        }
      },
      "payments": [
        {
          "status": "PAID",
          "type": "PRE-FULFILLMENT",
          "params": {
            "amount": "1500",
            "currency": "INR",
            "bank_code": "INB0004321",
            "bank_account_number": "1234002341"
          }
        }
      ]
    }
  }
}
```
Below is an example of an `on_confirm` callback
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_confirm",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "58d0a142-68e3-48d9-92e8-fbc791e44949",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "order": {
      "id": "b989c9a9-f603-4d44-b38d-26fd72286b38",
      "provider": {
        "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider",
        "descriptor": {
          "name": "Venky.Mahadevan@Bazaar"
        },
        "locations": [
          {
            "id": "./retail.kirana/ind.blr/1@tourism-bpp-infra2.becknprotocol.io.provider_location"
          }
        ]
      },
      "items": [
        {
          "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
          "descriptor": {
            "images": [
              {
                "url": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
              }
            ],
            "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow - Water bottle",
            "short_desc": "InstaCuppa Stainless Steel Thermos Flask Water Bottle with Sports Sipper Lid, Double Walled Vacuum Insulation",
            "long_desc": "<div> <ul> <li>ULTRA MODERN DESIGN - Our thermos bottle is crafted with a unique and modern design. Gone are the days of old and boring flasks. Guaranteed to impress your colleagues, friends & family.</li> <li>ADVANCED TEMPERATURE CONTROL – A double-wall, vacuum-insulated design helps lock in heat for up to 12 hours and cold for up to 24!</li> <li>ELIMINATES CONDENSATION – Offering improved grip and control, these innovative dual-layer bottles offer a slip-resistant surface that’s free of sweat and condensation..</li> <li>LEAK-PROOF and ECO-FRIENDLY – Remove, and clean, the large, screw on lid provides faster access to water inside and won’t spill a drop even when it’s tipped upside or put in your gym bag.</li> <li>The distress quilted jacket is a versatile fashion choice you can wear on any occasion. A style essential piece for Women which will reveal your strong sense of personality</li> </ul> <div> <p><b>Product Details</b></p> <ul> <li>Advanced Temperature Retention.This thermos water bottle ensures your beverages will remain hot or cold for a long time.Hot for up to 12 hours.Cold for up to 24 hours.</li> <li>Retains Original Flavors.Vacuum insulation ensures this travel thermos water bottle is airtight and retains the original flavor of your beverages.Also, this bottle is B.P.A Free.</li> <li>Premium Quality Materials.This stylish bottle is a double-walled vacuum insulated and made from premium 304-grade stainless steel - which makes this flask bottle.</li> </ul> </div>"
          },
          "category_ids": [
            "c1"
          ],
          "price": {
            "listed_value": "1200.0",
            "currency": "INR",
            "value": "1200.0"
          }
        }
      ],
      "fulfillments": [
        {
          "type": "Delivery",
          "stops": [
            {
              "location": {
                "gps": "13.2008459,77.708736",
                "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
                "city": {
                  "name": "Gangamuthanahalli"
                },
                "state": {
                  "name": "Karnataka"
                },
                "country": {
                  "code": "IND"
                },
                "area_code": "75001"
              },
              "contact": {
                "phone": "919246394908",
                "email": "nc.rehman@gmail.com"
              }
            }
          ],
          "customer": {
            "person": {
              "name": "Motiur Rehman"
            },
            "contact": {
              "phone": "919122343344"
            }
          }
        }
      ],
      "quote": {
        "price": {
          "currency": "INR",
          "value": "1500.0"
        },
        "breakup": [
          {
            "title": "base-price",
            "price": {
              "currency": "INR",
              "value": "1200.0"
            }
          },
          {
            "title": "taxes",
            "price": {
              "currency": "INR",
              "value": "300.0"
            }
          }
        ]
      },
      "billing": {
        "name": "Motiur Rehman",
        "phone": "9191223433",
        "email": "nc.rehman@gmail.com",
        "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
        "city": {
          "name": "Gangamuthanahalli"
        },
        "state": {
          "name": "Karnataka"
        }
      },
      "payments": [
        {
          "status": "NOT-PAID",
          "type": "PRE-FULFILLMENT",
          "params": {
            "amount": "1500",
            "currency": "INR",
            "bank_code": "INB0004321",
            "bank_account_number": "1234002341"
          }
        }
      ],
      "cancellation_terms": [
        {
          "cancellation_fee": {
            "amount": {
              "currency": "INR",
              "value": "100"
            }
          }
        }
      ]
    }
  }
}
```

## 1.3 Fulfillment of retail order
This section contains recommendations for implementing the APIs related to fulfilling a retail order

### 1.3.1 Recommendations for BPPs

#### 1.3.1.1 Sending status updates
- REQUIRED. The BPP MUST implement the `status` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.1.2 Updating a retail order
- REQUIRED. The BPP MUST implement the `update` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.1.3 Cancelling a retail order
- REQUIRED. The BPP MUST implement the `cancel` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST implement the `get_cancellation_reasons` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.1.4 Real-time tracking
- REQUIRED. The BPP MUST implement the `track` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.3.2 Recommendations for BAPs

#### 1.3.2.1 Receiving status updates
- REQUIRED. The BAP MUST implement the `on_status` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `status`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.2.2 Updating a retail order
- REQUIRED. The BAP MUST implement the `on_update` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `update`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.2.3 Cancelling a retail order
- REQUIRED. The BAP MUST implement the `on_cancel` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `cancel`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BAP MUST implement the `cancellation_reasons` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `get_cancellation_reasons`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.2.4 Real-time tracking
- REQUIRED. The BAP MUST implement the `on_track` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `track`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.3.3. Example Workflow
#### 1.3.3.1 Status Workflow
1. The Buyer app receives new updates on the order status from unsolicted /on_status call.
2. The Buyer requested for a status update.
3. The Buyer app calls /status endpoint of the seller app.
4. The Seller app provides the updated order object in /on_status call.

#### 1.3.3.2 Update Workflow
1. The Buyer choose to update the order ( For eg. Update Qty of any Item or Updating Delivery details).
2. The Buyer App calls /update endpoint of the seller app.
3. The Seller app update the order and return updated order object in /on_update call.
4. The Buyer will receive the refund, if total order amount is reduced or have to pay additional price if order amount is increased.

#### 1.3.3.3 Cancel Workflow
1. The Buyer choose to cancel the Order (Complete or partial).
2. The Buyer app call /get_cancellation_reason endpoint of the seller app.
3. The Seller app respond with cancellation reasons using /cancellation_reason endpoint.
4. The Buyer App renders the reasons and buyer chooses one and proceed with cancellation.
5. The Buyer app calls /cancel endpoint of the seller app.
6. The Seller app cancels the order and sends cancellation confirmation in /on_cancel endpoint.

#### 1.3.3.4 Tracking Workflow
1. The Buyer requested to track the delivery of the order.
2. The Buyer app calls /track endpoint of the seller app.
3. The Seller app respond with the tracking url using /on_track endpoint.
4. The Buyer app render the tracking url in the UI.
5. The Buyer is able to track the order delivery.

### 1.3.4 Example Requests

Below is an example of a `status` request
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "status",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "b8c1e69c-fbbc-439b-a5de-2adcc74fa0da",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "order_id": "b989c9a9-f603-4d44-b38d-26fd72286b38"
  }
}
```

Below is an example of an `on_status` callback
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_status",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "b8c1e69c-fbbc-439b-a5de-2adcc74fa0da",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "order": {
      "id": "b989c9a9-f603-4d44-b38d-26fd72286b38",
      "provider": {
        "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider",
        "descriptor": {
          "name": "Venky.Mahadevan@Bazaar"
        },
        "locations": [
          {
            "id": "./retail.kirana/ind.blr/1@tourism-bpp-infra2.becknprotocol.io.provider_location"
          }
        ]
      },
      "items": [
        {
          "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
          "descriptor": {
            "images": [
              {
                "url": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
              }
            ],
            "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow - Water bottle",
            "short_desc": "InstaCuppa Stainless Steel Thermos Flask Water Bottle with Sports Sipper Lid, Double Walled Vacuum Insulation",
            "long_desc": "<div> <ul> <li>ULTRA MODERN DESIGN - Our thermos bottle is crafted with a unique and modern design. Gone are the days of old and boring flasks. Guaranteed to impress your colleagues, friends & family.</li> <li>ADVANCED TEMPERATURE CONTROL – A double-wall, vacuum-insulated design helps lock in heat for up to 12 hours and cold for up to 24!</li> <li>ELIMINATES CONDENSATION – Offering improved grip and control, these innovative dual-layer bottles offer a slip-resistant surface that’s free of sweat and condensation..</li> <li>LEAK-PROOF and ECO-FRIENDLY – Remove, and clean, the large, screw on lid provides faster access to water inside and won’t spill a drop even when it’s tipped upside or put in your gym bag.</li> <li>The distress quilted jacket is a versatile fashion choice you can wear on any occasion. A style essential piece for Women which will reveal your strong sense of personality</li> </ul> <div> <p><b>Product Details</b></p> <ul> <li>Advanced Temperature Retention.This thermos water bottle ensures your beverages will remain hot or cold for a long time.Hot for up to 12 hours.Cold for up to 24 hours.</li> <li>Retains Original Flavors.Vacuum insulation ensures this travel thermos water bottle is airtight and retains the original flavor of your beverages.Also, this bottle is B.P.A Free.</li> <li>Premium Quality Materials.This stylish bottle is a double-walled vacuum insulated and made from premium 304-grade stainless steel - which makes this flask bottle.</li> </ul> </div>"
          },
          "category_ids": [
            "c1"
          ],
          "price": {
            "listed_value": "1200.0",
            "currency": "INR",
            "value": "1200.0"
          }
        }
      ],
      "fulfillments": [
        {
          "type": "Delivery",
          "stops": [
            {
              "location": {
                "gps": "13.2008459,77.708736",
                "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
                "city": {
                  "name": "Gangamuthanahalli"
                },
                "state": {
                  "name": "Karnataka"
                },
                "country": {
                  "code": "IND"
                },
                "area_code": "75001"
              },
              "contact": {
                "phone": "919246394908",
                "email": "nc.rehman@gmail.com"
              }
            }
          ],
          "customer": {
            "person": {
              "name": "Motiur Rehman"
            },
            "contact": {
              "phone": "919122343344"
            }
          },
          "state": {
            "descriptor": {
              "code": "PACKING",
              "short_desc": "Order is getting packed ..."
            },
            "updated_at": "2023-05-26T05:23:04.443Z"
          }
        }
      ],
      "quote": {
        "price": {
          "currency": "INR",
          "value": "1500.0"
        },
        "breakup": [
          {
            "title": "base-price",
            "price": {
              "currency": "INR",
              "value": "1200.0"
            }
          },
          {
            "title": "taxes",
            "price": {
              "currency": "INR",
              "value": "300.0"
            }
          }
        ]
      },
      "billing": {
        "name": "Motiur Rehman",
        "phone": "9191223433",
        "email": "nc.rehman@gmail.com",
        "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
        "city": {
          "name": "Gangamuthanahalli"
        },
        "state": {
          "name": "Karnataka"
        }
      },
      "payments": [
        {
          "status": "NOT-PAID",
          "type": "PRE-FULFILLMENT",
          "params": {
            "amount": "1500",
            "currency": "INR",
            "bank_code": "INB0004321",
            "bank_account_number": "1234002341"
          }
        }
      ],
      "cancellation_terms": [
        {
          "cancellation_fee": {
            "amount": {
              "currency": "INR",
              "value": "100"
            }
          }
        }
      ]
    }
  }
}
```
Below is an example of a `update` request

```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "update",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "090adec2-696d-47c9-8452-797a91e7bebc",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "order": {
      "id": "b989c9a9-f603-4d44-b38d-26fd72286b38",
      "fulfillments": [
        {
          "customer": {
            "contact": {
              "phone": "+91-8056475647"
            }
          }
        }
      ]
    },
    "update_target": "order.fulfillments[0].customer.contact.phone"
  }
}
```
Below is an example of a `on_update` callback

```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_update",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "090adec2-696d-47c9-8452-797a91e7bebc",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "order": {
      "id": "b989c9a9-f603-4d44-b38d-26fd72286b38",
      "provider": {
        "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider",
        "descriptor": {
          "name": "Venky.Mahadevan@Bazaar"
        },
        "locations": [
          {
            "id": "./retail.kirana/ind.blr/1@tourism-bpp-infra2.becknprotocol.io.provider_location"
          }
        ]
      },
      "items": [
        {
          "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
          "descriptor": {
            "images": [
              {
                "url": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
              }
            ],
            "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow - Water bottle",
            "short_desc": "InstaCuppa Stainless Steel Thermos Flask Water Bottle with Sports Sipper Lid, Double Walled Vacuum Insulation",
            "long_desc": "<div> <ul> <li>ULTRA MODERN DESIGN - Our thermos bottle is crafted with a unique and modern design. Gone are the days of old and boring flasks. Guaranteed to impress your colleagues, friends & family.</li> <li>ADVANCED TEMPERATURE CONTROL – A double-wall, vacuum-insulated design helps lock in heat for up to 12 hours and cold for up to 24!</li> <li>ELIMINATES CONDENSATION – Offering improved grip and control, these innovative dual-layer bottles offer a slip-resistant surface that’s free of sweat and condensation..</li> <li>LEAK-PROOF and ECO-FRIENDLY – Remove, and clean, the large, screw on lid provides faster access to water inside and won’t spill a drop even when it’s tipped upside or put in your gym bag.</li> <li>The distress quilted jacket is a versatile fashion choice you can wear on any occasion. A style essential piece for Women which will reveal your strong sense of personality</li> </ul> <div> <p><b>Product Details</b></p> <ul> <li>Advanced Temperature Retention.This thermos water bottle ensures your beverages will remain hot or cold for a long time.Hot for up to 12 hours.Cold for up to 24 hours.</li> <li>Retains Original Flavors.Vacuum insulation ensures this travel thermos water bottle is airtight and retains the original flavor of your beverages.Also, this bottle is B.P.A Free.</li> <li>Premium Quality Materials.This stylish bottle is a double-walled vacuum insulated and made from premium 304-grade stainless steel - which makes this flask bottle.</li> </ul> </div>"
          },
          "category_ids": [
            "c1"
          ],
          "price": {
            "listed_value": "1200.0",
            "currency": "INR",
            "value": "1200.0"
          }
        }
      ],
      "fulfillments": [
        {
          "type": "Delivery",
          "stops": [
            {
              "location": {
                "gps": "13.2008459,77.708736",
                "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
                "city": {
                  "name": "Gangamuthanahalli"
                },
                "state": {
                  "name": "Karnataka"
                },
                "country": {
                  "code": "IND"
                },
                "area_code": "75001"
              },
              "contact": {
                "phone": "919246394908",
                "email": "nc.rehman@gmail.com"
              }
            }
          ],
          "customer": {
            "person": {
              "name": "Motiur Rehman"
            },
            "contact": {
              "phone": "+91-8056475647"
            }
          },
          "state": {
            "descriptor": {
              "code": "PACKING",
              "short_desc": "Order is getting packed ..."
            },
            "updated_at": "2023-05-26T05:23:04.443Z"
          }
        }
      ],
      "quote": {
        "price": {
          "currency": "INR",
          "value": "1500.0"
        },
        "breakup": [
          {
            "title": "base-price",
            "price": {
              "currency": "INR",
              "value": "1200.0"
            }
          },
          {
            "title": "taxes",
            "price": {
              "currency": "INR",
              "value": "300.0"
            }
          }
        ]
      },
      "billing": {
        "name": "Motiur Rehman",
        "phone": "9191223433",
        "email": "nc.rehman@gmail.com",
        "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
        "city": {
          "name": "Gangamuthanahalli"
        },
        "state": {
          "name": "Karnataka"
        }
      },
      "payments": [
        {
          "status": "NOT-PAID",
          "type": "PRE-FULFILLMENT",
          "params": {
            "amount": "1500",
            "currency": "INR",
            "bank_code": "INB0004321",
            "bank_account_number": "1234002341"
          }
        }
      ],
      "cancellation_terms": [
        {
          "cancellation_fee": {
            "amount": {
              "currency": "INR",
              "value": "100"
            }
          }
        }
      ]
    }
  }
}
```
Below is an example of a `cancel` request
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "cancel",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "c53b2e4b-ff6d-434b-ba2c-329a3260b3e9",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "order_id": "b989c9a9-f603-4d44-b38d-26fd72286b38",
    "cancellation_reason_id": "4",
    "descriptor": {
      "short_desc": "Order delayed"
    }
  }
}
```
Below is an example of a `on_cancel` callback
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_cancel",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "c53b2e4b-ff6d-434b-ba2c-329a3260b3e9",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "order": {
      "id": "b989c9a9-f603-4d44-b38d-26fd72286b38",
      "provider": {
        "id": "./retail.kirana/ind.blr/33@tourism-bpp-infra2.becknprotocol.io.provider",
        "descriptor": {
          "name": "Venky.Mahadevan@Bazaar"
        },
        "locations": [
          {
            "id": "./retail.kirana/ind.blr/1@tourism-bpp-infra2.becknprotocol.io.provider_location"
          }
        ]
      },
      "items": [
        {
          "id": "./retail.kirana/ind.blr/247@tourism-bpp-infra2.becknprotocol.io.item",
          "descriptor": {
            "images": [
              {
                "url": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
              }
            ],
            "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow - Water bottle",
            "short_desc": "InstaCuppa Stainless Steel Thermos Flask Water Bottle with Sports Sipper Lid, Double Walled Vacuum Insulation",
            "long_desc": "<div> <ul> <li>ULTRA MODERN DESIGN - Our thermos bottle is crafted with a unique and modern design. Gone are the days of old and boring flasks. Guaranteed to impress your colleagues, friends & family.</li> <li>ADVANCED TEMPERATURE CONTROL – A double-wall, vacuum-insulated design helps lock in heat for up to 12 hours and cold for up to 24!</li> <li>ELIMINATES CONDENSATION – Offering improved grip and control, these innovative dual-layer bottles offer a slip-resistant surface that’s free of sweat and condensation..</li> <li>LEAK-PROOF and ECO-FRIENDLY – Remove, and clean, the large, screw on lid provides faster access to water inside and won’t spill a drop even when it’s tipped upside or put in your gym bag.</li> <li>The distress quilted jacket is a versatile fashion choice you can wear on any occasion. A style essential piece for Women which will reveal your strong sense of personality</li> </ul> <div> <p><b>Product Details</b></p> <ul> <li>Advanced Temperature Retention.This thermos water bottle ensures your beverages will remain hot or cold for a long time.Hot for up to 12 hours.Cold for up to 24 hours.</li> <li>Retains Original Flavors.Vacuum insulation ensures this travel thermos water bottle is airtight and retains the original flavor of your beverages.Also, this bottle is B.P.A Free.</li> <li>Premium Quality Materials.This stylish bottle is a double-walled vacuum insulated and made from premium 304-grade stainless steel - which makes this flask bottle.</li> </ul> </div>"
          },
          "category_ids": [
            "c1"
          ],
          "price": {
            "listed_value": "1200.0",
            "currency": "INR",
            "value": "1200.0"
          }
        }
      ],
      "fulfillments": [
        {
          "type": "Delivery",
          "stops": [
            {
              "location": {
                "gps": "13.2008459,77.708736",
                "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
                "city": {
                  "name": "Gangamuthanahalli"
                },
                "state": {
                  "name": "Karnataka"
                },
                "country": {
                  "code": "IND"
                },
                "area_code": "75001"
              },
              "contact": {
                "phone": "919246394908",
                "email": "nc.rehman@gmail.com"
              }
            }
          ],
          "customer": {
            "person": {
              "name": "Motiur Rehman"
            },
            "contact": {
              "phone": "919122343344"
            }
          },
          "state": {
            "descriptor": {
              "code": "CANCELLED",
              "short_desc": "Cancelled due to ..."
            },
            "updated_at": "2023-05-26T05:23:04.443Z"
          }
        }
      ],
      "quote": {
        "price": {
          "currency": "INR",
          "value": "1500.0"
        },
        "breakup": [
          {
            "title": "base-price",
            "price": {
              "currency": "INR",
              "value": "1200.0"
            }
          },
          {
            "title": "taxes",
            "price": {
              "currency": "INR",
              "value": "300.0"
            }
          }
        ]
      },
      "billing": {
        "name": "Motiur Rehman",
        "phone": "9191223433",
        "email": "nc.rehman@gmail.com",
        "address": "123, Terminal 1, Kempegowda Int'l Airport Rd, A - Block, Gangamuthanahalli, Karnataka 560300, India",
        "city": {
          "name": "Gangamuthanahalli"
        },
        "state": {
          "name": "Karnataka"
        }
      },
      "payments": [
        {
          "status": "NOT-PAID",
          "type": "PRE-FULFILLMENT",
          "params": {
            "amount": "1500",
            "currency": "INR",
            "bank_code": "INB0004321",
            "bank_account_number": "1234002341"
          }
        }
      ],
      "cancellation_terms": [
        {
          "cancellation_fee": {
            "amount": {
              "currency": "INR",
              "value": "100"
            }
          }
        }
      ]
    }
  }
}
```
Below is an example of a `track` request

```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "track",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "fd585827-7607-4ce8-b257-9597b0ed0f52",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "order_id": "b989c9a9-f603-4d44-b38d-26fd72286b38"
  }
}
```
Below is an example of a `on_track` callback

```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_track",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "fd585827-7607-4ce8-b257-9597b0ed0f52",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "tracking": {
      "url": "https://abc/tracking/201f6fa2-a2f7-42e7-a2e5-8947398747",
      "status": "active"
    }
  }
}
```
## 1.4 Post-fulfillment of retail order
This section contains recommendations for implementing the APIs after fulfilling a retail order

### 1.4.1 Recommendations for BPPs

#### 1.4.1.1 Rating and Feedback
- REQUIRED. The BPP MUST implement the `rating` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST implement the `get_rating_categories` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.4.1.2 Providing Support
- REQUIRED. The BPP MUST implement the `support` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.4.2 Recommendations for BAPs

#### 1.4.2.1 Rating and Feedback
- REQUIRED. The BAP MUST implement the `on_rating` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `rating`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BAP MUST implement the `rating_categories` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `get_rating_categories`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.4.2.2 Providing Support
- REQUIRED. The BAP MUST implement the `on_support` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `support`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.4.3 Example Workflow
#### 1.4.3.1 Rating & Feedback Workflow
1. Buyer wants to give rating to the seller.
2. Buyer App calls /get_rating_categories API.
3. Seller app provides the available rating categories using /rating_categories API to the buyer app.
4. Buyer App render the rating category.
5. Buyer provide the rating on a particular category (i.e, Order, Item or Delivery).
6. Buyer app send the rating to the seller app using /rating API.
7. Seller app acknowledge the rating and provide feedback link to the buyer app using /on_rating API.
8. Buyer might choose to fill the feedback form.

#### 1.4.3.2 Support Workflow
1. Buyer faces any issue w.r.t to the order or delivery.
2. Buyer creates a support request.
3. Buyer app send /support call to the seller app.
4. Seller app acknowledge the support request and send support contact details using /on_support API call.

### 1.4.4 Example Requests

Below is an example of a `get_rating_categories` request
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "get_rating_categories",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "b0c5b14f-a7db-4329-b7e2-42a5f6486085",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  }
}

```

Below is an example of an `rating_categories` callback
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "rating_categories",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "b0c5b14f-a7db-4329-b7e2-42a5f6486085",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "rating_categories": [
      "Item",
      "provider",
      "Order"
    ]
  }
}
```

Below is an example of a `rating` request

```
{
    "context": {
        "domain": "local-retail",
        "location": {
            "country": {
              "code": "IND"
            },
            "city": {
              "code": "std:080"
            }
          },
        "action": "rating",
        "version": "1.1.0",
        "bap_id": "farm-fresh-bap-id",
        "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
        "bpp_id": "farm-fresh-bpp-subId",
        "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
        "message_id": "b0c5b14f-a7db-4329-b7e2-42a5f6486085",
        "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
        "timestamp": "2023-11-06T10:14:10.295Z",
        "ttl": "PT10M"
    },
    "message": {
        "ratings": [
            {
                "id": "b989c9a9-f603-4d44-b38d-26fd72286b38",
                "rating_category": "Order",
                "value": "8"
            }
        ]
    }
}
```
Below is an example of an `on_rating` callback
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_rating",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "b0c5b14f-a7db-4329-b7e2-42a5f6486085",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "feedback_form": {
      "form": {
        "url": "https://inds-network-bpp.becknprotocol.io/feedback/portal"
      }
    }
  }
}
```

Below is an example of a `support` request
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "support",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "1f507212-23fe-4acc-a34e-d25b459ad105",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "support": {
      "ref_id": "894789-43954",
      "phone": "+91 4444444444",
      "email": "me@gmail.com"
    }
  }
}
```

Below is an example of an `on_support` callback
```
{
  "context": {
    "domain": "local-retail",
    "location": {
      "country": {
        "code": "IND"
      },
      "city": {
        "code": "std:080"
      }
    },
    "action": "on_support",
    "version": "1.1.0",
    "bap_id": "farm-fresh-bap-id",
    "bap_uri": "https://55a6-124-123-32-28.ngrok-free.app",
    "bpp_id": "farm-fresh-bpp-subId",
    "bpp_uri": "https://4e21-124-123-32-28.ngrok-free.app",
    "message_id": "1f507212-23fe-4acc-a34e-d25b459ad105",
    "transaction_id": "8100d125-76a7-4588-88be-81b97657cd09",
    "timestamp": "2023-11-06T10:14:10.295Z",
    "ttl": "PT10M"
  },
  "message": {
    "support": {
      "ref_id": "d4975df5-b18c-4772-80ad",
      "callback_phone": "+91 8765495826",
      "phone": "+91 9876543298",
      "email": "abcd.support@support.com"
    }
  }
}
```