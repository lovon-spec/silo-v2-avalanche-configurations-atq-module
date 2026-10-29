# Silo V2 configurations on Avalanche

This ATQ module emits the configuration contracts for Silo Finance V2 markets on Avalanche, chain43114. These are configuration contracts, distinct from lending vaults and their share tokens.

## Specific source documentation

Silo Finance's own [snapshot documentation](https://github.com/silo-finance/lenders-snapshot/blob/58dbfc612906307137c305d56adf9f1a7ea61dce/scripts/lender-snapshot/README.md)
describes the `subgraph_url` configuration and identifies `snapshot_lenders.py`
as its canonical configuration. That [documented configuration](https://github.com/silo-finance/lenders-snapshot/blob/58dbfc612906307137c305d56adf9f1a7ea61dce/scripts/lender-snapshot/snapshot_lenders.py)
explicitly selects Avalanche's subgraph
`6NLL9WmjPYima4NhUpNEWeDu5eBXFuhP9QheRXkoJXR5`. This is a reference to that
specific subgraph, without inferring authorization for other publications by
the same address. The project also identifies these chain endpoints as legacy
V2 sources in its [borrower tooling](https://github.com/silo-finance/actions/blob/0e0a4f79772a176846e181483d1f22adf5b26948/scripts/pull_borrowers/fetch_legacy_borrowers.py).

The [named subgraph](https://thegraph.com/explorer/subgraphs/6NLL9WmjPYima4NhUpNEWeDu5eBXFuhP9QheRXkoJXR5?chain=arbitrum-one&view=About)
resolved on 2026-10-29 to deployment
`QmXa7JQ3ggss8JjrrYYt1J4EdHiaB1RdLhxArr1wEnfBXb`. The module queries that
explicit deployment through The Graph gateway, with the supplied key only in
the authorization header. Recheck the named source before filing an item.

## Retrieval and naming

Every Market row is retrieved at one block hash through complete ascending unique-address-ID cursor pagination and a final metadata seal. Source market indices repeat for each configuration, so the unique Market ID supplies the cursor. All rows are visited.

Each configuration is referenced by both of its lending-vault market rows. They produce the same deterministic tag, with the vaults ordered by source market index0/1. A repeated configuration address is consolidated only when the entire tag agrees; conflicting identities, IDs, indices or vault pairs throw. No separate configuration is removed.

Names use the actual source Silo ID and the configuration role. Long names use ellipsis; a name collision uses the full configuration address. Notes identify both lending vaults. The UI link names the protocol application and does not claim that a legacy market is currently usable or listed.

Each query, including its pacing delay and response-body read, has a 30-second deadline. The complete run has a ten-minute deadline. A deadline throws instead of returning a partial set; an AbortController also cancels native fetch/body work. The local race remains bounded even if a supplied fetch implementation ignores cancellation.

Use Yarn, yarn build and yarn test. All logic is in src/main.mts, exporting only async returnTags with exactly two string arguments. MIT licensed, derived from our original module.
