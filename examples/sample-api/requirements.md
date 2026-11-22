# Create product (invented sample requirement)

Version: none. Date: none.

An admin creates a product through `POST /product`.

## Fields

| Field | Description |
| :-- | :-- |
| name | Product name. Required. |
| price | Unit price. Required. Must be greater than 0. |
| category | One of STATIONERY, TOYS, BOOKS. Required. |
| supplierId | The supplier of the product. Optional. If given, the supplier must be ACTIVE. |
| description | Free text. Optional. |

## Behaviour

1. A valid request creates the product with status `DRAFT`.
2. The API returns `200` with the new `id`.
3. A request without a token is rejected.
4. A supplier that is not ACTIVE is rejected.

## Not stated

The document does not give the error code or message for any rejected request. It does not give a maximum length for `name`.

## Planted gaps (for the person trying the skill)

- The Swagger marks no field as required. The document states which are required, so the skill cites the document as the source of each required flag. It must not use the body-level `required: true` in the Swagger as a field marker.
- The success status is `201` in the Swagger and `200` in the document: a conflict.
- `supplierId` ACTIVE rule is in the document only: a gap in the Swagger, not a conflict.
- No error codes, no messages, no length limit: the skill must write `OPEN` and ask.
