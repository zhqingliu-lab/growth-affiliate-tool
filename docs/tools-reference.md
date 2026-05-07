# Tools reference

Six MCP tools, all read-only. Every tool that takes a time period uses the same
[`periodType` codes](period-type.md).

| #  | Tool                                                            | Purpose                          |
| -- | --------------------------------------------------------------- | -------------------------------- |
| 1  | [`affiliate-pro-performance-summary`](#1-performance-summary)   | Aggregate metrics                |
| 2  | [`affiliate-pro-invitee-list`](#2-invitee-list)                 | Paginated invitee list           |
| 3  | [`affiliate-pro-invitee-detail`](#3-invitee-detail)             | Single invitee deep dive          |
| 4  | [`affiliate-pro-link-list`](#4-link-list)                       | Your invite links                |
| 5  | [`affiliate-pro-sub-affiliate-list`](#5-sub-affiliate-list)     | Sub-affiliates in MLRS network   |
| 6  | [`affiliate-pro-co-inviter-list`](#6-co-inviter-list)           | Channels where you co-invite     |

---

## 1. Performance summary

**Tool name:** `affiliate-pro-performance-summary`

Aggregate performance for the connected affiliate over a chosen time window.

### Parameters

| Param           | Type   | Required | Default | Description                                                                  |
| --------------- | ------ | :------: | :-----: | ---------------------------------------------------------------------------- |
| `periodType`    | int    | No       | `5`     | Time window — see [`period-type.md`](period-type.md)                          |
| `periodStart`   | string | When `periodType=4` | — | Custom start date, `YYYY-MM-DD`                                               |
| `periodEnd`     | string | When `periodType=4` | — | Custom end date, `YYYY-MM-DD`                                                 |
| `inviteeSource` | int    | No       | `0`     | Invitee source filter; `0` = all                                              |
| `pageType`      | int    | No       | `1`     | Page type; `1` = home. **Do not pass `2` — it returns 400.** See [FAQ](faq.md) |

### Return fields

```json
{
  "invitees": "1854",
  "traders": "836",
  "firstTimeTrader": "836",
  "volume": "6171516631.12",
  "commission": "521232.80",
  "deposit": "37192317.15",
  "tradersDer": "769",
  "tradersSpot": "567",
  "tradersBsc": "1",
  "volumeDer":  "5999283747.12",
  "volumeSpot": "174305846.31",
  "volumeBsc":  "143.59",
  "commissionDer":  "494157.98",
  "commissionSpot": "27317.57",
  "commissionBsc":  "0",
  "lastUpdatedTime": 1777516383000
}
```

Every monetary aggregate is broken down into Spot / Derivatives / BSC.

---

## 2. Invitee list

**Tool name:** `affiliate-pro-invitee-list`

Paginated list of your direct invitees with their trading, deposit, and KYC stats.

### Parameters

| Param                   | Type   | Required | Default | Description                                                                                  |
| ----------------------- | ------ | :------: | :-----: | -------------------------------------------------------------------------------------------- |
| `page`                  | int    | ✅       | —       | Page number (starts at `1`)                                                                  |
| `pageSize`              | int    | No       | `10`    | Items per page; max `50`                                                                     |
| `periodType`            | int    | No       | `5`     | Time window — see [`period-type.md`](period-type.md)                                          |
| `periodStart`           | string | When `periodType=4` | — | Custom start date, `YYYY-MM-DD`                                                               |
| `periodEnd`             | string | When `periodType=4` | — | Custom end date, `YYYY-MM-DD`                                                                 |
| `type`                  | int    | No       | `0`     | Trade type filter — `0`=all, `1`=Spot, `2`=Derivatives, `3`=BSC                              |
| `orderItem`             | int    | No       | `1`     | Sort field — `1`=Join time, `3`=Deposit, `5`=Volume, `6`=Fees, `7`=Commission                 |
| `orderType`             | int    | No       | `2`     | Sort order — `1`=Asc, `2`=Desc                                                                |
| `searchWord`            | string | No       | —       | Substring match against UID or channel name                                                  |
| `descendantAffiliateId` | int    | No       | —       | Filter to invitees attributed to a specific sub-affiliate UID                                |
| `hasDeposit`            | bool   | No       | —       | `true` = only invitees who have deposited; `false` = only ones who have not                  |
| `hasTrade`              | bool   | No       | —       | `true` = only invitees who have ever traded; `false` = only ones who have not                |
| `kycVerifiedStatus`     | int    | No       | —       | `1`=Unverified, `2`=Verified                                                                  |
| `countryCode`           | string | No       | —       | KYC country filter (ISO-2)                                                                    |
| `channelName`           | string | No       | —       | Filter to a specific channel                                                                  |

### Return fields (per row)

```json
{
  "uid": "...",
  "channelName": "CRYPTO1818",
  "kycCountry": "CN",
  "kycVerifiedStatus": 2,
  "kycVerifiedTime": 1755440968000,
  "feeTierLevel": 6,
  "feeTierLevelName": "VIP 2",
  "rebateRatio": 20,
  "isCompliant": true,
  "deposited": 10329631.36,
  "fees": 347153.57,
  "totalReward": 104146.07,
  "totalTradingVolume": 1186843492.29,
  "firstTimeTraded": 1755496800000,
  "relateTime": 1755424349000
}
```

`deposited`, `fees`, `totalReward`, and `totalTradingVolume` are **scoped to the requested
`periodType`**. To get lifetime totals, pass `periodType=5` (the default).

---

## 3. Invitee detail

**Tool name:** `affiliate-pro-invitee-detail`

Deep dive on a single invitee, by external UID.

### Parameters

| Param | Type   | Required | Description                                  |
| ----- | ------ | :------: | -------------------------------------------- |
| `uid` | string | ✅       | The invitee's external UID (from list above) |

### Return fields

```json
{
  "uid": "743072917935893796",
  "affiliateCode": "CRYPTO1818",
  "region": "China",
  "level": "Lv1",
  "inviteeLevel": "2",
  "inviteeRebateRate": "0.2",
  "joinTime": "1755424349000",
  "kycTime": "1755440968744",
  "firstTradeTime": "1755496800000",
  "depAmt": "10329631.35",
  "withdrawalAmount": "9795977.54",
  "totalTradingVolume": "1186843492.29",
  "totalCommission": "104146.07",
  "accFee": "347153.56",
  "volMonth": "37.04"
}
```

`volMonth` is the calendar-month-to-date trading volume — useful for spotting users whose
activity dropped this month even though their lifetime numbers look healthy.

---

## 4. Link list

**Tool name:** `affiliate-pro-link-list`

Your invite links with cumulative invitee count, trader count, and commission.

### Parameters

| Param         | Type | Required | Default | Description                                              |
| ------------- | ---- | :------: | :-----: | -------------------------------------------------------- |
| `page`        | int  | No       | `1`     | Page number                                              |
| `pageSize`    | int  | No       | `10`    | Items per page; max `50`                                 |
| `channelType` | int  | No       | `1`     | `1`=Standard links, `2`=Co-inviter links                  |
| `status`      | int  | No       | `0`     | `0`=All (default sort), `1`=Active only                  |

### Return fields (per link)

Channel name, invite URL, commission rate, invitee discount %, invitee count, trader count,
cumulative commission.

---

## 5. Sub-affiliate list

**Tool name:** `affiliate-pro-sub-affiliate-list`

Your sub-affiliates in the MLRS (multi-level referral system) network.

### Parameters

| Param        | Type   | Required | Default | Description                                                          |
| ------------ | ------ | :------: | :-----: | -------------------------------------------------------------------- |
| `page`       | int    | No       | `1`     | Page number                                                          |
| `pageSize`   | int    | No       | `10`    | Items per page; max `50`                                             |
| `periodType` | int    | No       | `5`     | Time window — see [`period-type.md`](period-type.md)                 |
| `type`       | int    | No       | `0`     | Trade type — `0`=all, `1`=Spot, `2`=Derivatives, `3`=BSC              |
| `orderItem`  | int    | No       | —       | Sort field, same codes as `affiliate-pro-invitee-list`                |
| `orderType`  | int    | No       | `2`     | `1`=Asc, `2`=Desc                                                     |
| `searchWord` | string | No       | —       | Search by sub-affiliate UID                                           |

### Return fields (per row)

Sub-affiliate UID, invitee count, trader count, trading volume, fees, commission.

---

## 6. Co-inviter list

**Tool name:** `affiliate-pro-co-inviter-list`

Channels where you are listed as a co-inviter (i.e. you share commission on those links).

### Parameters

| Param        | Type   | Required | Default | Description                          |
| ------------ | ------ | :------: | :-----: | ------------------------------------ |
| `page`       | int    | No       | `1`     | Page number                          |
| `pageSize`   | int    | No       | `10`    | Items per page; max `50`             |
| `status`     | int    | No       | `0`     | `0`=All                              |
| `orderType`  | int    | No       | `2`     | `1`=Asc, `2`=Desc                     |
| `searchWord` | string | No       | —       | Search by channel name                |

### Return fields (per row)

Channel name, your commission share, invitee stats, channel status.

---

## Notes shared across all tools

- All numeric strings are **decimal strings** — preserve precision when parsing.
- `relateTime`, `firstTimeTraded`, `kycVerifiedTime`, `lastUpdatedTime` are Unix epoch
  **milliseconds**.
- The endpoint is rate-limited. Bursts of >10 requests per second can return `429 Too Many
  Requests` (`code: 50011`); space your calls or back off on 429.
- Token lifetime is approximately **1 hour**. Most clients refresh transparently; if you see
  401s, trigger your client's MCP reconnect / refresh.
