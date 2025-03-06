---
title: 'Zpravodaj „Bitcoin Optech” č. 344'
permalink: /cs/newsletters/2025/03/07/
name: 2025-03-07-newsletter-cs
slug: 2025-03-07-newsletter-cs
type: newsletter
layout: newsletter
lang: cs
---
This week's newsletter announces the disclosure of a vulnerability
affecting old versions of LND and summarizes a discussion about the Bitcoin
Core Project's priorities.  Also included are our regular sections
describing discussion related to consensus changes, announcing new
releases and release candidates, and summarizing notable changes to
popular Bitcoin infrastructure software.

## Novinky

- **Odhalení opravené zranitelnosti v LND umožňující krádež:** Matt
  Morehouse zaslal do fóra Delving Bitconi [příspěvek][morehouse failback]
  s odhalením [zodpovědně nahlášené][topic responsible disclosures] zranitelnosti,
  která postihovala verze LND před 0.18. Doporučuje se upgrade na 0.18 nebo
  (ideálně) na [aktuální verzi][lnd current]. Útočník, který s obětí sdílí
  kanál a který může přimět oběť k restartování svého uzlu v určitou dobu,
  může klamem přinutit LND k platbě i refundaci jednoho HTLC, což útočníkovi
  umožní ukrást téměř kompletní hodnotu kanálu.

  Morehouse poznamenává, že všechny ostatní LN implementace nezávisle tuto
  zranitelnost objevily a opravily, některé už v roce 2018 (viz [zpravodaj
  č. 17][news17 cln2000], _angl._). LN specifikace však správné chování
  nepopisuje (může dokonce doporučovat nesprávné chování), Morehouse
  proto [otevřel PR][bolts #1233] s aktualizací specifikace.

- **Diskuze o prioritách Bitcoin Core:** [vlákno][poinsot pri] ve fóru
  Delvin Bitcoin odkázalo na několik blogových příspěvků od Antoine
  Poinsota o budoucnosti projektu Bitcoin Core. V [prvním][poinsot pri1]
  příspěvku popisuje Poinsot výhody dlouhodobých cílů a náklady ad-hoc
  rozhodnutí. Ve [druhém][poinsot pri2] příspěvku soudí, že „by měl být
  Bitcoin Core robustní oporou bitcoinové sítě, která hledá rovnováhu
  mezi bezpečností Bitcoin Core a přidáváním nových funkcí posilňujících
  a vylepšujících celou síť.“ Ve [třetím][poinsot pri3] příspěvku doporučuje
  rozdělit existující projekt na tři části: uzel, peněženku a grafické
  rozrhraní. Díky několikaletému usílí stojícímu za podprojektem rozdělení
  aplikace do více běžících procesů (ve zpravodaji byl tento podprojekt
  poprvé zmíněn v [č. 39][news39 multiprocess], _angl._) je tato možnost
  nyní nadosah.

  Anthony Towns [pochybuje][towns pri], že by běh ve více procesech opravdu
  umožnil efektivní rozdělení, neboť jednotlivé komponenty by nadále zůstaly
  úzce provázané. Mnoho změn učiněných v jednom projektu by si vyžádalo
  změny i v ostatních. Bylo by však jasnou výhrou, pokud by byly funkce,
  které nevyžadují uzel, extrahovány do nějaké knihovny nebo nástroje,
  jež by mohly být udržovány nezávisle. Dále popisuje, jak někteří lidé
  používají uzly s mezivrstvou, která jim usnadňuje připojit jejich peněženky
  k vlastním uzlům pomocí indexů blockchainu (v podstatě [osobních
  prohlížečů bloků][topic block explorers]); Bitcoin Core v minulosti
  odmítl přidat podobnou funkci přímo do svého uzlu. Nakonec [poznamenává][towns
  pri2], že dle něj „poskytování funkce peněženky (ve větší míře) a GUI
  (v menší míře) je způsob, jak držet směr bitcoinu jako použitelného
  decentralizovanou skupinou hackerů oproti něčemu, co můžou používat
  jen finanční velrbyby nebo zavedené korporace ochotné investovat ve
  velkém.”

  David Harding vyjádřil [obavy][harding pri], že změna zaměření
  hlavního projektu pouze na kód konsenzu a P2P přeposílání ztíží běžným
  uživatelům používání plného uzlu pro validování svých vlastních
  příchozích transakcí. Žádá Poinsota a jiné přispěvatele, aby namísto toho
  zvážili zaměřit se na snadnost používání Bitcoin Core. Popisuju sílu
  validování plným uzlem: ti, kteří provozují plné uzly validující převládající
  množství ekonomické aktivity, drží schopnost definovat bitcoinová pravidla
  konsenzu. Na příkladu ukazuje, že i pouhá 30minutová změna výběru
  vynucovaných pravidel může vést k politicky trvalému zničení opěvovaných
  vlastností bitcoinu, jako je omezení na 21 miliónů BTC. Věří, že každodenní
  uživatelé mají silnější motivaci zachovat vlastnosti bitcoinu než
  organizace provozující uzly pro své klienty. Pokud si vývojáři Bitcoin
  Core cení současných pravidel konsenzu, je dle Hardinga pro bezpečenost
  stejně důležité usnadňovat běžným uživatelům osobně ověřovat své transakce,
  jako je bránění a odstraňování chyb, které by mohly vést k vážným
  zranitelnostem.

## Diskuze o změnách konsenzu

_Měsíční rubrika shrnující návrhy a diskuze o změnách pravidel
bitcoinového konsenzu._

- **Průvodce forkováním bitcoinu:** Anthony Towns ve fóru Delving
  Bitcoin [ohlásil][towns bfg] průvodce způsobu budování konsenzu komunity
  pro změny bitcoinových pravidel konsenzu. Rozděluje sociální konsenzus
  do čtyř fází: výzkum a vývoj, zkoumání pokročilými uživateli, vyhodnocování
  průmyslem a posuzování investory. Dále se krátce zabývá technickými
  kroky, které přichází na konci procesu v rámci aktivace změn v bitcoinovém
  software.

  Jeho příspěvek poznamenává, že „je to jen průvodce cestou spolupráce, kde
  děláte změny, které zlepší život všechy, a víceméně všichni se na tom
  nakonec shodnou.” Též varuje, že „průvodce nahlíží z dost velké výšky.”

- **Aktualizace BIP360 pay-to-quantum-resistant-hash (P2QRH):** vývojář
  Hunter Beast zaslal do emailové skupiny Bitcoin-Dev [aktualizaci][beast p2qrh]
  svého výzkumu [kvantové rezistence][topic quantum resistance] pro [BIP360][].
  Změnil obsah seznamu kvantově bezpečných algoritmů, které navrhuje používat,
  hledá advokáta vývoje schématu pay-to-taproot-hash (P2TRH, viz též [zpravodaj
  č. 141][news141 p2trh], _angl._) a zvažuje zaměřit se na stejnou bezpečnostní
  úroveň, jakou poskytuje současný bitcoin (NIST Ⅱ), namísto vyšší úrovně
  (NIST Ⅴ), která by vyžadovala víc blokového prostoru a procesoru během
  validace. Jeho příspěvek obdržel několik reakcí.

- **Private block template marketplace to prevent centralizing MEV:** Matt
  Corallo and developer 7d5x9 [posted][c7 mev] to Delving Bitcoin about
  allowing parties to bid in public markets for selected space within
  miner block templates.  For example, "I’ll pay X [BTC] to include
  transaction Y as long as it comes before any other transactions which
  interact with the smart contract identified by Z".  This is something
  that transaction creators on Bitcoin already want for various
  protocols, such as certain [colored coin protocols][topic client-side
  validation], and it's likely to become even more desirable in the
  future as new protocols are developed (including proposals requiring
  consensus changes such as certain [covenants][topic covenants]).

  If the service of preferential transaction ordering within block
  templates is not provided by a trust-reduced public market, it's
  probable that it will instead be provided by large miners, who will
  compete with users of the various protocols.  This will require the
  miners obtain large amounts of capital and technical sophistication,
  likely leading to them earning significantly higher profits than smaller
  miners without those capabilities.  This will centralize mining and
  allow the large miners to censor Bitcoin transactions more easily.

  The developers propose providing trust reduction by allowing miners to
  work on blinded block templates whose complete transactions aren't
  revealed to the miner until they've produced sufficient proof of work
  to publish the block.  The developers propose two mechanisms for
  achieving this without requiring any consensus changes:

  - **Trusted block templates:** a miner connects to a marketplace, selects
    the bids it wants to include in a block, and asks the marketplace to
    construct a block template.  The marketplace responds with a block
    header, coinbase transaction, and partial merkle branch that allow
    the miner to generate proof of work for that template without
    learning its exact contents.  If the miner does produce the network
    _target_ amount of proof of work, it submits the header and coinbase
    transaction to the marketplace, which verifies the proof of work,
    adds it to the block template, and
    broadcasts the block.  The marketplace might include a transaction
    paying the miner in the block template or it might pay the miner
    separately at a later time.

  - **Trusted execution environments:** miners obtain a device with a
    [TEE][] secure enclave, connect to marketplaces and select bids they
    want to include in their blocks, and receive the transactions in
    those bids encrypted to the TEE's enclave key.  The block template
    is constructed within the TEE and the TEE provides the host operating
    system with the header, coinbase transaction, and partial merkle
    branch.  If the target proof of work is generated, the miner
    provides it to the TEE, which verifies it and returns the full
    decrypted block template for the miner to add to the header and broadcast.  Again, the block
    template might include a transaction paying the miner from a UTXO
    belonging to the marketplace or the marketplace might pay the miner
    later.

  Both schemes would effectively require multiple competing
  marketplaces, with the proposal noting that the expectation would be
  that some community members and organizations would operate marketplaces
  on a non-profit basis to help preserve decentralization against the
  dominance of a single trusted marketplace.

## Releases and release candidates

_New releases and release candidates for popular Bitcoin infrastructure
projects.  Please consider upgrading to new releases or helping to test
release candidates._

- [Core Lightning 25.02][] is a release of the next major version of
  this popular LN node.  It includes support for [peer storage][topic
  peer storage] (used for storing encrypted penalty transactions that can be
  retrieved and decrypted to provide a type of [watchtower][topic
  watchtowers]) in addition to other improvements and bug fixes.

## Notable code and documentation changes

_Notable recent changes in [Bitcoin Core][bitcoin core repo], [Core
Lightning][core lightning repo], [Eclair][eclair repo], [LDK][ldk repo],
[LND][lnd repo], [libsecp256k1][libsecp256k1 repo], [Hardware Wallet
Interface (HWI)][hwi repo], [Rust Bitcoin][rust bitcoin repo], [BTCPay
Server][btcpay server repo], [BDK][bdk repo], [Bitcoin Improvement
Proposals (BIPs)][bips repo], [Lightning BOLTs][bolts repo],
[Lightning BLIPs][blips repo], [Bitcoin Inquisition][bitcoin inquisition
repo], and [BINANAs][binana repo]._

- [Eclair #3019][] modifies a node’s behavior to favor a remote commitment
  transaction seen in the mempool over broadcasting a local one during a
  peer-initiated unilateral close. Previously, the node would broadcast its
  local commitment, potentially causing a race between the two transactions.
  Opting for the remote commitment transaction is beneficial to the local node because
  it avoids local `OP_CHECKSEQUENCEVERIFY` (CSV) [timelock][topic timelocks] delays
  and eliminates the need for additional transactions from the local node to resolve pending
  [HTLCs][topic htlc].

- [Eclair #3016][] introduces low-level methods for creating Lightning
  transactions in [simple taproot channels][topic simple taproot channels],
  without making any functional changes. These methods are generated with
  [miniscript][topic miniscript], and differ from those outlined in the [BOLTs
  #995][] specification.

- [LDK #3342][] adds a `RouteParametersConfig` struct that enables users to
  customize routing parameters for [BOLT12][topic offers] invoice payments.
  Previously limited to `max_total_routing_fee_msat`, the new struct now
  includes [`max_total_cltv_expiry_delta`][topic cltv expiry delta],
  `max_path_count`, and `max_channel_saturation_power_of_half`. This change
  brings the [BOLT12][] parameter setting in line with that of [BOLT11][].

- [Rust Bitcoin #4114][] lowers the minimum non-witness transaction size from 85
  bytes to 65 bytes, aligning with Bitcoin Core’s policy (see Newsletter
  [#222][news222 minsize] and [#232][news232 minsize]). This change allows for
  more minimal transactions, such as those with one input and one `OP_RETURN`
  output.

- [Rust Bitcoin #4111][] adds support for the new [P2A][topic ephemeral anchors]
  standard output type, introduced in Bitcoin Core 28.0 (see Newsletter
  [#315][news315 p2a]).

- [BIPs #1758][] updates [BIP374][], which defines Discrete Log Equality Proofs
  ([DLEQ][topic dleq]) (see newsletter [#335][news335 dleq]), by incorporating the message
  field into the `rand` computation. This change prevents the potential leakage
  of `a` (the private key) that could occur if two proofs were constructed with
  the same `a`, `b`, and `g` but with different messages and an all-zero `r`.

- [BIPs #1750][] updates [BIP329][], which defines a format for exporting
  [wallet labels][topic wallet labels], by adding optional fields associated
  with addresses, transactions and outputs. A JSON type fix is also included.

- [BIPs #1712][] and [BIPs #1771][] add [BIP3][], replacing [BIP2][] by making
  several updates to the BIP process. Changes include reducing the status field
  values to four (from nine), allowing a Draft BIP to be marked Closed by anyone
  after a year of no progress if authors do not confirm ongoing work, permitting
  BIPs to remain in Complete status indefinitely, enabling continuous updates to
  process BIPs like this one, reassigning some editorial decisions from BIP
  editors to authors or the repository’s audience, eliminating the comment
  system, and requiring a BIP to be on-topic to receive a number, along with
  several updates to the BIP format and preamble.

{% include snippets/recap-ad.md when="2025-03-11 15:30" %}
{% include references.md %}
{% include linkers/issues.md v=2 issues="3019,3016,3342,4114,4111,1758,1750,1712,1771,1233,995" %}
[Core Lightning 25.02]: https://github.com/ElementsProject/lightning/releases/tag/v25.02
[news39 multiprocess]: /en/newsletters/2019/03/26/#bitcoin-core-10973
[news141 p2trh]: /en/newsletters/2021/03/24/#we-could-add-a-hash-style-address-after-taproot-is-activated
[poinsot pri]: https://delvingbitcoin.org/t/antoine-poinsot-on-bitcoin-cores-priorities/1470/
[poinsot pri1]: https://antoinep.com/posts/core_project_direction/
[poinsot pri2]: https://antoinep.com/posts/stating_the_obvious/
[poinsot pri3]: https://antoinep.com/posts/bitcoin_core_scope/
[towns pri]: https://delvingbitcoin.org/t/antoine-poinsot-on-bitcoin-cores-priorities/1470/3
[towns pri2]: https://delvingbitcoin.org/t/antoine-poinsot-on-bitcoin-cores-priorities/1470/15
[harding pri]: https://delvingbitcoin.org/t/antoine-poinsot-on-bitcoin-cores-priorities/1470/10
[towns bfg]: https://delvingbitcoin.org/t/bitcoin-forking-guide/1451
[beast p2qrh]: https://mailing-list.bitcoindevs.xyz/bitcoindev/8797807d-e017-44e2-b419-803291779007n@googlegroups.com/
[c7 mev]: https://delvingbitcoin.org/t/best-worst-case-mevil-response/1465
[tee]: https://en.wikipedia.org/wiki/Trusted_execution_environment
[news17 cln2000]: /en/newsletters/2018/10/16/#c-lightning-2000
[morehouse failback]: https://delvingbitcoin.org/t/disclosure-lnd-excessive-failback-exploit/1493
[lnd current]: https://github.com/lightningnetwork/lnd/releases
[news222 minsize]: /en/newsletters/2022/10/19/#minimum-relayable-transaction-size
[news232 minsize]: /en/newsletters/2023/01/04/#bitcoin-core-26265
[news315 p2a]: /en/newsletters/2024/08/09/#bitcoin-core-30352
[news335 dleq]: /en/newsletters/2025/01/03/#bips-1689
