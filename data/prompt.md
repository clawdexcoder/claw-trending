# DEX Trending Token Data — Field Reference

You are receiving a live feed of trending token pairs from decentralised exchanges (DEXs). Each record represents a single trading pair. Tokens may be meme coins, utility tokens, governance tokens, stablecoins, wrapped assets, or any other token type. Use this guide to interpret each field correctly.

---

## Pair Identity

- **chainId**: The blockchain this pair is trading on (e.g. `solana`, `ethereum`, `bsc`, `base`, `arbitrum`). Determines which network the contracts live on.
- **dexId**: The specific decentralised exchange or AMM where this pair is listed (e.g. `raydium`, `uniswap`, `pumpswap`, `pancakeswap`).
- **pairAddress**: The on-chain contract address of the liquidity pool. Unique identifier for this exact trading pair on this exact DEX.

## Tokens in the Pair

Every pair has two sides: a **base token** (the token being traded) and a **quote token** (what it's priced against).

- **baseToken.address**: Contract address of the token being traded.
- **baseToken.name**: Human-readable name (e.g. "Uniswap", "Bonk", "Wrapped Bitcoin").
- **baseToken.symbol**: Ticker symbol (e.g. "UNI", "BONK", "WBTC").
- **baseToken.decimals**: Number of decimal places the token uses on-chain. Important for interpreting raw amounts.
- **quoteToken**: Same structure. Typically a major asset like SOL, ETH, USDC, or USDT.
- **quoteTokenSymbol**: Shorthand for the quote token symbol.

## Price

- **price**: Current price of the base token denominated in the quote token (e.g. `0.00002751` SOL).
- **priceUsd**: Current price in US dollars. Use this for cross-chain or cross-pair comparisons.

## Transaction Activity

Transaction counts across time windows. Higher counts indicate more active trading.

- **txns.m5 / h1 / h6 / h24**: Number of buy and sell transactions in the last 5 minutes, 1 hour, 6 hours, and 24 hours.
  - `buys` and `sells` are separate counts.
  - A buy/sell ratio significantly above 1.0 suggests buying pressure; below 1.0 suggests selling pressure.

## Unique Wallets

- **buyers**: Count of unique wallet addresses that bought in each time window.
- **sellers**: Count of unique wallet addresses that sold in each time window.
- **makers**: Count of unique wallet addresses that traded (bought or sold) in each time window.
  - Compare `buyers` vs `sellers` to gauge sentiment. Many more unique buyers than sellers can indicate organic interest.
  - If `txns` is high but `makers` is low, a small number of wallets are trading repeatedly (possible bot activity or wash trading).

## Volume (USD)

- **volume.m5 / h1 / h6 / h24**: Total trading volume in USD across each time window.
- **volumeBuy**: USD volume from buy transactions only.
- **volumeSell**: USD volume from sell transactions only.
  - Compare volumeBuy vs volumeSell for net flow direction.
  - Rapidly increasing volume relative to prior windows may signal a breakout or dump.

## Price Change (%)

- **priceChange.m5 / h1 / h6 / h24**: Percentage price change over each window.
  - Positive = price went up. Negative = price went down.
  - Large divergence between short-term (m5) and long-term (h24) may indicate a reversal or acceleration.

## Liquidity

- **liquidity.usd**: Total liquidity in the pool in USD. Higher liquidity means less slippage on trades.
- **liquidity.base**: Amount of the base token in the pool.
- **liquidity.quote**: Amount of the quote token in the pool.
  - Low liquidity (under ~$10k) means the price is easily manipulable.
  - Very low liquidity relative to volume is a warning sign (potential rug pull or exit scam).

## Valuation

- **marketCap**: Estimated market capitalisation in USD (circulating supply x price).
- **fdv**: Fully diluted valuation — total supply x price. If fdv equals marketCap, 100% of tokens are in circulation.
  - Large gap between marketCap and fdv means there are locked/unvested tokens that could dilute holders later.

## Pair Metadata

- **pairCreatedAt**: Unix timestamp (milliseconds) of when this liquidity pair was first created on-chain. Convert to a date to assess token age. Very new pairs (minutes/hours old) carry higher risk.

## Project Profile

- **cmsProfile.description**: Short description of the project provided by the token creators.
- **cmsProfile.links**: Array of associated URLs — may include website, Twitter/X, Telegram, Discord, etc.
- **cmsProfile.iconId / headerId**: Image identifiers for the token's icon and banner.
- **cmsProfile.nsfw**: Whether the project has been flagged as containing adult content.

## Launchpad Info

- **launchpad.progress**: Percentage of the bonding curve or launch phase completed. `100` means the token has fully graduated and migrated to a live DEX.
- **launchpad.creator**: Wallet address of the account that created the token.
- **launchpad.migrationDex**: Which DEX the token migrated to after launch (e.g. `pumpswap`, `raydium`).
- **launchpad.meta.id**: Identifier for the launch platform used (e.g. `pumpfun`).

## Extraction Metadata

- **extractedAt**: Unix timestamp (milliseconds) of when this data was scraped. Use this to assess data freshness.
- **pageNumber**: Which page of the trending list this pair appeared on (page 1 = highest trending).
- **positionOnPage**: Zero-indexed position within that page. Lower = higher ranking.

---

## Signals and Patterns to Watch For

- **High buy/sell ratio + rising price + increasing volume**: Strong bullish momentum.
- **Falling price + high volume**: Potential sell-off or panic. Could be a dump.
- **Low makers relative to txns**: Likely bot activity or wash trading. Treat volume figures with caution.
- **Liquidity << volume**: Pool may be getting drained. High risk.
- **pairCreatedAt very recent + large priceChange.h24**: Classic pump pattern. Exercise caution.
- **fdv >> marketCap**: Significant token unlocks ahead could create sell pressure.
- **launchpad.progress < 100**: Token is still in its bonding curve phase and has not yet migrated to open market trading.
