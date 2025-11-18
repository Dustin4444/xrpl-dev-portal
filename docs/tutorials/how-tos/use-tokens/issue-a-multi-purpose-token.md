---
seo:
  description: Issue a Multi-Purpose Token (MPT) with arbitrary metadata on the XRP Ledger.
metadata:
  indexPage: true
labels:
  - Multi-Purpose Token
  - MPT
  - Token Issuance
---
# Issue a Multi-Purpose Token (MPT)

A [Multi-Purpose Token (MPT)](../../../concepts/tokens/fungible-tokens/multi-purpose-tokens.md) lets you to quickly access powerful, built-in tokenization features on the XRP Ledger with minimal code.

This tutorial shows you how to issue an MPT with on-chain metadata such as the token's ticker, name, or description, encoded according to the MPT [metadata schema](../../../concepts/tokens/fungible-tokens/multi-purpose-tokens.md#metadata-schema) defined in [XLS-89](https://xls.xrpl.org/xls/XLS-0089-multi-purpose-token-metadata-schema.html).

## Goals

By the end of this tutorial, you will be able to:

- Issue a new MPToken on the XRP Ledger.
- Encode or decode token metadata following MPT standards best practices.

## Prerequisites

To complete this tutorial, you should:

- Have a basic understanding of the XRP Ledger.
- Have an XRP Ledger client library set up in your development environment. This page provides examples for the following:
	- **JavaScript** with the [xrpl.js library](https://github.com/XRPLF/xrpl.js). See [Get Started Using JavaScript](../../javascript/build-apps/get-started.md) for setup steps.
    - **Python** with the [xrpl-py library](https://github.com/XRPLF/xrpl-py). See [Get Started Using Python](../../python/build-apps/get-started.md) for setup steps.

## Source Code

You can find the complete source code for this tutorial's example in the [code samples section of this website's repository](https://github.com/XRPLF/xrpl-dev-portal/tree/master/_code-samples/issue-mpt-with-metadata).

## Steps

The example in this tutorial demonstrates how to issue a sample [US Treasury bill (T-bill)](https://www.treasurydirect.gov/research-center/history-of-marketable-securities/bills/t-bills-indepth/) as an MPT on the XRP Ledger.

### 1. Install dependencies

{% tabs %}
{% tab label="Javascript" %}
From the code sample folder, use npm to install dependencies:

```bash
npm install xrpl
```
{% /tab %}

{% tab label="Python" %}
From the code sample folder, install dependencies using pip:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```
{% /tab %}
{% /tabs %}

### 2. Set up client and fund issuer wallet

Import the client library, instantiate a client to connect to the XRPL, and fund a new wallet to act as the token issuer.

{% tabs %}

{% tab label="Javascript" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/js/issue-mpt-with-metadata.js" language="js" before="// Define metadata as JSON" /%}
{% /tab %}

{% tab label="Python" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/py/issue-mpt-with-metadata.py" language="py" before="# Define metadata as JSON" /%}
{% /tab %}

{% /tabs %}

### 3. Define and encode MPT metadata

The metadata you provide is what distinguishes your token from other MPTs. Define the JSON metadata as shown in the following code snippet:

{% tabs %}

{% tab label="Javascript" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/js/issue-mpt-with-metadata.js" language="js" from="// Define metadata as JSON" before="// Encode the metadata" /%}
{% /tab %}

{% tab label="Python" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/py/issue-mpt-with-metadata.py" language="py" from="# Define metadata as JSON" before="# Encode the metadata" /%}
{% /tab %}

{% /tabs %}

The metadata schema defined in XLS-89 supports both long field names (`ticker`, `name`, `desc`) and compact short keys (`t`, `n`, `d`). To save space on the ledger, it’s recommended to use short key names. The MPT metadata field has a 1024-byte limit, so using compact keys allows you to include more information.

The SDK libraries provide utility functions to encode or decode the metadata for you, so you don't have to. If long field names are provided in the JSON, the **encoding utility function** automatically shortens them to their compact key equivalents before encoding. Similarly, when decoding, the **decoding utility function** converts the short keys back to their respective long names.

To encode the metadata:

{% tabs %}

{% tab label="Javascript" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/js/issue-mpt-with-metadata.js" language="js" from="// Encode the metadata" before="// Define the transaction" /%}
{% /tab %}

{% tab label="Python" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/py/issue-mpt-with-metadata.py" language="py" from="# Encode the metadata" before="# Define the transaction" /%}
{% /tab %}

{% /tabs %}

{% admonition type="warning" name="Warning" %}
The encoding function will raise an error if the input is not a valid JSON object.
{% /admonition %}

### 4. Prepare the MPTokenIssuanceCreate transaction

To issue the MPT, create an `MPTokenIssuanceCreate` transaction object. Specify the issuer, asset scale, maximum amount, flags, and the encoded metadata.

| Field               | Description  |
|:------------------- |:------ |
| `TransactionType`   | The type of transaction, in this case `MPTokenIssuanceCreate`. |
| `Account`           | The wallet address of the account that is issuing the MPT, in this case the `issuer`. |
| `AssetScale`        | The number of decimal places for the token. |
| `MaximumAmount`     | The maximum supply of the token to be issued. |
| `TransferFee`       | The transfer fee (if any) to charge for token transfers. |
| `Flags`             | Flags to set token permissions. For this example the **Can Transfer** and **Can Trade** flags are enabled. See [MPTokenIssuanceCreate Flags](../../../references/protocol/transactions/types/mptokenissuancecreate.md#mptokenissuancecreate-flags) for all available flags. |
| `MPTokenMetadata`   | The hex-encoded metadata for the token. |

{% tabs %}

{% tab label="Javascript" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/js/issue-mpt-with-metadata.js" language="js" from="// Define the transaction" before="// Sign and submit the transaction" /%}
{% /tab %}

{% tab label="Python" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/py/issue-mpt-with-metadata.py" language="py" from="# Define the transaction" before="# Sign and submit the transaction" /%}
{% /tab %}

{% /tabs %}

### 5. Submit MPTokenIssuanceCreate transaction

Sign and submit the `MPTokenIssuanceCreate` transaction to the ledger.

{% tabs %}

{% tab label="Javascript" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/js/issue-mpt-with-metadata.js" language="js" from="// Sign and submit the transaction" before="// Check transaction results" /%}
{% /tab %}

{% tab label="Python" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/py/issue-mpt-with-metadata.py" language="py" from="# Sign and submit the transaction" before="# Check transaction results" /%}
{% /tab %}

{% /tabs %}

Some important considerations about token metadata when you submit the transaction:

- If you provide metadata that exceeds the 1024-byte limit, the transaction will fail with an error.

- If the metadata does not conform to the XLS-89 standards, the transaction stills succeeds, but your token may not be compatible with wallets and applications that expect valid MPT metadata.
  
  The SDK libraries provide a warning to help you diagnose why your metadata is not compliant. For example, you might see a warning like the following:

  ```sh
  MPTokenMetadata is not properly formatted as JSON as per the XLS-89d standard. 
  While adherence to this standard is not mandatory, such non-compliant MPToken's 
  might not be discoverable by Explorers and Indexers in the XRPL ecosystem.
  - ticker/t: should have uppercase letters (A-Z) and digits (0-9) only. Max 6 characters recommended.
  - name/n: should be a non-empty string.
  - icon/i: should be a non-empty string.
  - asset_class/ac: should be one of rwa, memes, wrapped, gaming, defi, other.
  ```

### 6. Check transaction result

Verify that the transaction succeeded and retrieve the MPT issuance ID.

{% tabs %}

{% tab label="Javascript" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/js/issue-mpt-with-metadata.js" language="js" from="// Check transaction results" before="// Look up MPT Issuance entry" /%}
{% /tab %}

{% tab label="Python" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/py/issue-mpt-with-metadata.py" language="py" from="# Check transaction results" before="# Look up MPT Issuance entry" /%}
{% /tab %}

{% /tabs %}

A `tesSUCCESS` result indicates that the transaction is successful and the token has been created.

### 7. Confirm MPT issuance and decode metadata

Look up the MPT issuance entry in the validated ledger and decode the metadata to verify it matches your original input.

{% tabs %}

{% tab label="Javascript" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/js/issue-mpt-with-metadata.js" language="js" from="// Look up MPT Issuance entry" /%}
{% /tab %}

{% tab label="Python" %}
{% code-snippet file="/_code-samples/issue-mpt-with-metadata/py/issue-mpt-with-metadata.py" language="py" from="# Look up MPT Issuance entry" /%}
{% /tab %}

{% /tabs %}

The decoding utility function converts the metadata back to a JSON object and expands the compact key names back to their respective long names.

## See Also

- **Concepts**:
	- [Multi-Purpose Tokens (MPT)](../../../concepts/tokens/fungible-tokens/multi-purpose-tokens.md)
- **References**:
	- [MPTokenIssuance entry][]
	- [MPTokenIssuanceCreate transaction][]

{% raw-partial file="/docs/_snippets/common-links.md" /%}
