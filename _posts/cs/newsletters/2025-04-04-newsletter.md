---
title: 'Zpravodaj „Bitcoin Optech” č. 348'
permalink: /cs/newsletters/2025/04/04/
name: 2025-04-04-newsletter-cs
slug: 2025-04-04-newsletter-cs
type: newsletter
layout: newsletter
lang: cs
---
This week's newsletter FIXME

## Novinky

- **Educational and experimental-based secp256k1 implementation:**
  Sebastian Falbesoner, Jonas Nick, and Tim Ruffing [posted][fnr secp]
  to the Bitcoin-Dev mailing list to announce a Python
  [implementation][secp256k1lab] of various functions related to the
  cryptography used in Bitcoin.  They warn that the implementation is
  "INSECURE" (caps in original) and "intended for prototyping,
  experimentation, and education."

  They also note that reference and test code for several BIPs
  ([340][bip340], [324][bip324], [327][bip327], and [352][bip352])
  already includes "custom and sometimes subtly diverging
  implementations of secp256k1."  They hope to improve this situation
  going forward, perhaps starting with an upcoming BIP for ChillDKG (see
  [Newsletter #312][news312 chilldkg]).

## Diskuze o změnách konsenzu

_Měsíční rubrika shrnující návrhy a diskuze o změnách pravidel
bitcoinového konsenzu._

- **Multiple discussion about quantum computer theft and resistance:**
  několik diskuzí zkoumalo, jak by bitcoineři reagovali, kdyby
  schopnosti kvantových počítačů umožnily krádež bitcoinů.
  
  - *Měly by být zranitelné bitcoiny zničeny?* Jameson Lopp [zaslal][lopp
    destroy] do emailové skupiny Bitcoin-Dev několik důvodů pro zničení
    bitcoinů náchylných na kvantovou krádež po nastolení cesty ke
    [kvantové odolnosti][topic quantum resistance] a ponechání dostatečné
    dobu pro osvojení uživateli. Mezi některé důvody patří:
    
    - *Sdílená preference:* Lopp věří, že většina lidí by raději preferovala, aby
      byly jejich prostředky zničeny, než ukradeny někým s rychlým kvantovým počítačem,
      obzvláště když by to byl zloděj z „úzké skupinky privilegovaných lidí,
      kteří se ke kvantovým počítačům dostanou brzy.”

    - *Obecná škoda:* mnoho z ukradených bitcoinů by byly buď ztracené mince
      nebo mince určené pro dlouhodobé držení. Na druhou stranu by zloději
      rychle ukradené bitcoiny utratili, čímž by se snížila kupní síla ostatních
      bitcoinů (podobně jako u inflace peněžní zásoby). Poznamenává, že
      nižší kupní síla bitcoinů vede k nižším příjmům těžařů, což snižuje bezpečnost
      sítě a (dle Loppa) i ochotu obchodníků bitcoin přijímat.

    - *Minimální výhody:* i když by umožnění krádeže mohlo vést  although allowing theft could be
      used to fund the development of quantum computing, stealing coins
      does not provide any direct benefit to honest participants in the
      Bitcoin protocol.

    - *Argument from clear deadlines:* nobody can know far in advance
      the date at which someone with a quantum computer can begin
      stealing bitcoins, but a specific date at which quantum-vulnerable
      coins will be destroyed can be announced far in advance with
      perfect precision.  That clear deadline will provide more
      incentive for users to re-secure their bitcoins in time, ensuring fewer total coins
      are lost.

    - *Argument from miner incentives:* as noted above, quantum theft
      would likely reduce miner income.  A persistent majority of
      hashrate can censor spending of quantum-vulnerable bitcoins, which
      they might do even if the rest of Bitcoiners prefer a different
      outcome.

    Lopp also provides several arguments against destruction of
    vulnerable Bitcoins, but he concludes in favor of destruction.

    Nagaev Boris [asks][boris timelock] whether UTXOs that are
    [timelocked][topic timelocks] beyond the upgrade deadline should
    also be destroyed.  Lopp notes existing pitfalls of long timelocks
    and says he personally gets "a bit nervous locking funds for more
    than a year or two."

  - *Bezpečné doložení vlastnictví UTXO odhalením SHA256 předobrazu:*
    Martin Habovštiak zasladl do emailové skupiny Bitcoin-Dev [příspěvek][habovstiak
    gfsig] s myšlenkou, která by komukoliv umožnila doložit kontrolu
    nad nějakým UTXO, i kdyby nebyly ECDSA a [Schnorrovy podpisy][topic
    schnorr signatures] bezpečné (např. po příchodu rychlých kvantových
    počítačů). Pokud by UTXO obsahovalo SHA256 (nebo jiný
    kvantově bezpečný) commitment k předobrazu, který nikdy předtím
    odhalen nebyl, mohl by být pro zabránění kvantové krádeže použit
    vícekrokový protokol (v kombinaci se změnou konsezu) odhalující tento
    předobraz. Jedná se v základu o stejný nápad popsaný v [dřívějším návrhu][ruffing
    gfsig] Tima Ruffinga (viz [zpravodaj č. 141][news141 gfsig]), který se
    obecně nazývá [podpisové schéma Guy Fawkes][Guy Fawkes signature scheme].
    Jedná se také o variantu [schématu][back crsig], které Adam Back
    vymyslel v roce 2013 pro zlepšení ochrany proti cenzurujícím těžařům.
    Ve stručnosti funguje protokol takto:

    1. Alice obdrží prostředky na výstup, který nějakým způsobem vytváří SHA256
       závazek. Může to být přímo hašovaný výstup (P2PKH, P2SH, P2PWPKH, P2WSH)
       nebo [P2TR][topic taproot] výstup se skriptem.

    2. Pokud Alice obdrží několik plateb na stejný výstupní skript, musí
       se ujistit, že buď neutratí ani jednu z nich, dokud nebude připravena
       utratit všechny (určitě nutné pro P2PKH a P2WSH, pravděpodobně
       v praxi potřebné též pro P2SH a P2WSH), nebo musí zajistit,
       že alespoň jeden předobraz zůstane po jejích platbách neodhalený
       (snadné s P2TR platbou klíčem oproti platbě skriptem).

    3. Je-li Alice připravena provést platbu, běžným způsobem vytvoří transakci,
       avšak nezveřejní ji. Dále získá nějaký bitcoin s kvantově bezpečným podpisovým
       algoritmem na zaplacení poplatků.

    4. V transakci posílající některé z kvantově bezpečných bitcoinů vytvoří
       závazek kvantově nezabezpečeným bitcoinům, které chce poslat, a také
       zavázek nezvřejněné transakci (stále ji neodhaluje). Počká na dostatečný
       počet potvrzení transakce.

    5. Je-li Alicina transakce již dostatečně hluboko, odhalí předobraz a kvantově
       nezabezpečnou platbu.

    6. Uzly v síti hledají první transakci, která tomuto předobrazu zavazuje.
       Zavazuje-li tato transakce Alicině kvantově nezabezpečené platbě, její
       platbu provedou. Jinak nedělají nic.

    Tyto kroky zajistí, aby Alice nemusela odhalovat kvantově slabé informace,
    dokud si není jista, že její verze platby bude upřednostněna před
    pokusem o platbu nějakého provozovatelem kvantového počítače. Přesnější
    popis protokolu lze nalézt v Ruffingově [příspěvku][ruffing gfsig] z roku 2018.
    Věříme, že takový protokol by mohl být nasazen jako soft fork (avšak nasazení
    se ve vlákně neprobíralo).

    Habovštiak říká, že by bitcoiny, které je možné bezpečně utratit
    použitím tohoto protokolu (např. pokud jejich předobrazy ještě nebyly
    odhaleny), neměly být zničeny, i kdyby se komunita rozhodla pro zničení
    kvantově zranitelných bitcoinů. Dále tvrdí, že možnost v naléhavých případech
    bezpečně utratit bitcoin snižuje urgentnost nasazení kvantově odolného
    schématu v blízké budoucnosti.

    Lloyd Fournier [říká][fournier gfsig]: „jestli se tento přístup přijme,
    myslím, že hlavní věcí pro uživatele je přesunout se na taprootovou
    peněženku,“ pro její schopnost platbami klíčem pod současnými pravidly
    konsenzu včetně případů [opakovaného použití adresy][topic output linking],
    ale i pro odolnost vůči kvantové krádeži, pokud by byly platby klíčem
    později zakázány.

    V jiném vlákně (viz další položku) Pieter Wuille [poznamenal][wuille
    nonpublic], že UTXO zranitelná vůči kvantové krádeži také obsahují
    klíče, které nebyly veřejně použité, ale které jsou známé více
    stranám, jako jsou různé formy vícenásobného podpisu (LN, [DLCs][topic
    dlc] či služby svěřenectví).

  - *Návrh BIPu na zničení kvantově nezabezpečených bitcoinů:* Agustin Cruz
    zaslal do emailové skupiny Bitcoin-Dev [příspěvek][cruz qramp] o [návrhu
    BIPu][cruz bip], který popisuje několik obecných možností procesu
    zničení bitcoinů, které nejsou zabezpečené vůči kvantové krádeži
    (pokud by se stala hrozbou). Cruz říká, že „vynucením lhůty pro migraci
    poskytujeme právoplatným majitelům jasnou a pevně danou příležitost
    zabezpečit své prostředky vynucenou migrací v dostatečném předstihu
    a s robustními ochranami; pro dloudobé zabezpečení bitcoinu je to
    zároveň realistické i nezbytné.”

    Jen málo diskuze v tomto vlákně se soustředilo na návrh BIPu. Podobně jako
    ve vlákně Jamesona Loppa (viz výše) se většina zaměřovala na otázku, zda je ničení
    kvantově nezabezpečených bitcoinů dobrý nápad.

- **Několik diskuzí o soft forku CTV+CSFS:** několik konverzací zkoumalo rozličné
  aspekty soft forků pro přidání opkódů [OP_CHECKTEMPLATEVERIFY][topic op_checktemplateverify]
  (CTV) a [OP_CHECKSIGFROMSTACK][topic op_checksigfromstack] (CSFS).

  - *Kritika motivace CTV:* Anthony Towns [zaslal][towns ctvmot] kritiku
    motivace [BIP119][], která by dle něj byla přidáním CTV s CSFS
    do bitcoinu podryta. Několik dní po začátku diskuze přinesl autor BIP119
    aktualizaci odstraňující většinu (možná i všechny) kontroverzních
    výroků. Ve [zpravodaji č. 347][news347 bip119] jsme přinesli souhrn
    této změny, viz též [starší verzi][bip119 prechange] BIP119. Mezi
    diskutovaná témata patřilo:

    - *CTV+CSFS umožňuje vytváření nekonečných kovenantů:*
      CTV's motivation said, "Covenants have historically been widely
      considered to be unfit for Bitcoin because they are too complex to
      implement and risk reducing the fungibility of coins bound by
      them.  This BIP introduces a simple covenant called a *template*
      which enables a limited set of highly valuable use cases without
      significant risk.  BIP119 templates allow for **non-recursive**
      fully-enumerated covenants with no dynamic state" (emphasis in
      original).

      Towns describes a script using both CTV and CSFS, and links to a
      [transaction][mn recursive] using it on the MutinyNet [signet][topic
      signet], that can only be spent by sending the same amount of
      funds back to the script itself.  Although there was some debate
      about definitions, the author of CTV has [previously
      described][rubin recurse] a functionally identical construction as
      a recursive covenant and Optech followed that convention in its
      summary of that conversation (see [Newsletter #190][news190
      recursive]).

      Olaoluwa Osuntokun [defended][osuntokun enum] CTV's motivation
      related to scripts using it remaining "fully-enumerated" and "with
      no dynamic state".  This appears to be similar to an
      [argument][rubin enumeration] the author of CTV (Jeremy Rubin)
      made in 2022, where he called the type of pay-to-self covenant
      Towns designed "recursive but fully enumerated".  Towns
      [countered][towns enum] that adding CSFS undermines the
      claimed benefit of full enumeration.  He asked for either the CTV
      or CSFS BIPs to be updated to describe any "use cases that are
      somehow both scary and still prevented by the combination of CTV
      and CSFS".  This may have been [done][ctv spookchain] in the
      recent update to BIP119, which describes a "self-reproducing
      automata (colloquially called SpookChains)" that would be possible
      using [SIGHASH_ANYPREVOUT][topic sighash_anyprevout] but which is
      not possible using CTV+CSFS.

    - *Tooling for CTV and CSFS:* Towns [noted][towns ctvmot] that he
      found it difficult to use existing tools to develop his recursive
      script described above, indicating a lack of readiness for
      deployment.  Osuntokun [said][osuntokun enum] the tooling he uses
      is "pretty straight forward".  Neither Towns nor Osuntokun
      mentioned what tools they used.  Nadav Ivgi [provided][ivgi minsc]
      an example using his [Minsc][] language and said that he's "been
      working on improving Minsc to make these kind of things easier.
      It supports Taproot, CTV, PSBT, Descriptors, Miniscript, raw
      Script, BIP32, and more." Although he admits "much of it is still
      undocumented".

    - *Alternatives:* Towns compares CTV+CSFS to both his Basic Bitcoin
      Lisp Language ([bll][topic bll]) and [Simplicity][topic
      simplicity], which would provide an alternative scripting
      language.  Antoine Poinsot [suggests][poinsot alt] that an
      alternative language that's easy to reason about may be less risky
      than a small change to the current system, which is hard to reason
      about.  Developer Moonsettler [argues][moonsettler express] that
      incrementally introducing new features to Bitcoin script makes it
      safer to add more features later, as each increase in expressivity
      makes it less likely that we'll encounter a surprise.

      Both Osuntokun and James O'Beirne [criticize][osuntokun enum] the
      [readiness][obeirne readiness] of bll and Simplicity in comparison
      to CTV and CSFS.

  - *CTV+CSFS benefits:* Steven Roose [posted][roose ctvcsfs] to Delving
    Bitcoin to suggest adding CTV and CSFS to Bitcoin as a first step to
    other changes that would increase expressivity further.  Most of the
    discussion focused on qualifying the possible benefits or CTV, CSFS,
    or both of them together.  This included:

    - *DLCs:* both CTV and CSFS individually can reduce the number of
      signatures needed to create [DLCs][topic dlc], especially DLCs for
      signing large numbers of variants of a contract (e.g., a BTC-USD
      price contract denominated in increments of one dollar).  Antoine
      Poinsot [linked][poinsot ctvcsfs1] to a recent
      [announcement][10101 shutdown] of a DLC service provider shutting
      down as evidence that Bitcoin users don't seem too interested in
      DLCs and linked to a [post][nick dlc] a few months ago from Jonas
      Nick that said, "DLCs have not achieved meaningful adoption on
      Bitcoin, and their limited use doesn't appear to stem from
      performance limitations." Replies linked to other still-functional
      DLC service providers, including one that [claims][lava 30m] to
      have raised "$30M in financing".

    - *Vaults:* CTV simplifies the implementation of [vaults][topic vaults]
      that are possible today on Bitcoin using presigned transactions
      and (optionally) private key deletion.  Anthony Towns [argues][towns
      vaults] that this type of vault isn't very interesting.  James O'Beirne
      [counters][obeirne ctv-vaults] that CTV, or something similar, is
      a prerequisite for building more advanced types of vaults, such as
      his [BIP345][] `OP_VAULT` vaults.

    - *Accountable computing contracts:* CSFS can eliminate many steps
      in [accountable computing contracts][topic acc] such as BitVM by
      replacing their current need to perform Script-based lamport
      signatures.  CTV may be able to reduce some additional signature
      operations.  Poinsot again [asks][poinsot ctvcsfs1] about whether
      there's significant demand for BitVM.  Gregory Sanders
      [replies][sanders bitvm] that he would find it interesting for
      bidirectional bridging of tokens as part of Shielded [client-side
      validation][topic client-side validation] (see [Newsletter
      #322][news322 csv-bitvm]).  However, he also notes that neither
      CTV nor CSFS significantly improve the trust model of BitVM,
      whereas other changes might be able to reduce trust or reduce the
      number of expensive operations in other ways.

    - *Improvement in Liquid timelock script:* James O'Beirne
      [relayed][obeirne liquid] comments from two Blockstream engineers
      that CTV would, in his words, "drastically improve the
      [Blockstream] Liquid timelock-fallback script that requires coins
      to be [moved] on a periodic basis."  After requests for
      clarification, former Blockstream engineer Sanket Kanjalkar
      [explained][kanjalkar liquid] that the benefit could be a
      significant savings in onchain transaction fees.  O'Beirne also [shared][poelstra
      liquid] additional details from Andrew Poelstra, Blockstream's
      Director of Research.

    - *LN-Symmetry:* CTV and CSFS together can be used to implement a
      form of [LN-Symmetry][topic eltoo], which eliminates some of the
      downsides of [LN-Penalty][topic ln-penalty] channels currently
      used in LN and may allow the creation of channels with more than
      two parties, which might improve liquidity management and onchain
      efficiency.  Gregory Sanders, who implemented an experimental
      version of LN-Symmetry (see [Newsletter #284][news284 lnsym])
      using [SIGHASH_ANYPREVOUT][topic sighash_anyprevout] (APO),
      [notes][sanders versus] that the CTV+CSFS version of LN-Symmetry
      isn't as featureful as the APO version and requires making
      tradeoffs.  Anthony Towns [adds][towns nonrepo] that nobody is
      known to have updated Sanders's experimental code for APO to run
      on modern software and use recently introduced relay features such
      as [TRUC][topic v3 transaction relay] and [ephemeral
      anchors][topic ephemeral anchors], much less has anyone ported the
      code to use CTV+CSFS, limiting our ability to evaluate that
      combination for LN-Symmetry.

      Poinsot [asks][poinsot ctvcsfs1] whether implementing LN-Symmetry
      would be a priority for LN developers if a soft fork
      made it possible.  Quotes from two Core Lightning
      developers (also co-authors of the paper introducing what we now
      call LN-Symmetry) indicated that it was a priority for them.  By
      comparison, LDK lead developer Matt Corallo [previously said][corallo
      eltoo], "I don't find [LN-Symmetry] all that interesting in terms
      of 'we need to get this done'".

    - *Ark:* Roose is the CEO of a business building an [Ark][topic ark]
      implementation.  He says, "CTV is a game-changer for Ark [...] the
      benefits of CTV to the user experience are undeniable, and it is
      without doubt that both [Ark] implementations will utilize CTV as
      soon as it is available."  Towns [noted][towns nonrepo] that
      nobody has implemented Ark with either APO or CTV for testing;
      Roose wrote [code][roose ctv-ark] doing that shortly thereafter,
      calling it "remarkably straightforward" and saying that it passed
      the existing implementation's integration tests.  He quantified
      some of the improvements: if they switched to CTV, "we could net
      remove about 900 lines of code [...] and reduce our own round
      protocol to [two] instead of three, [plus] the bandwidth
      improvement for not having to pass around signing nonces and
      partial signatures."

      Roose would later start a separate thread to discuss the
      benefits of CTV for users of Ark (see our summary below).

  - *Benefit of CTV to Ark users:* Steven Roose [posted][roose
    ctv-for-ark] to Delving Bitcoin a short description of the
    [Ark][topic ark] protocol currently deployed to [signet][topic
    signet], called [coventless Ark][clark doc] (clArk), and how the
    availability of the [OP_CHECKTEMPLATEVERIFY][topic
    op_checktemplateverify] (CTV) opcode could make a [covenant][topic
    covenants]-using version of the protocol more appealing to users when
    it's eventually deployed to mainnet.

    <!-- FIXME:harding update async payments topic page to be more
    general; it currently focuses on LN -->

    One design goal for Ark is to allow [async payments][topic async
    payments]: payments made when the receiver is offline.  In clArk,
    this is achieved by the spender plus an Ark server extending the
    spender's existing chain of presigned transactions, allowing the
    receiver to ultimately accept exclusive control over the funds.  The
    payment is called an Ark [out-of-round][oor doc] payment (_arkoor_).
    When the receiver comes online, they can choose what they want to
    do:

    - *Exit, after a delay:* broadcast the entire chain of presigned
      transactions, exiting the [joinpool][topic joinpools] (called an
      _Ark_).  This requires waiting for the expiry of a timelock agreed
      to by the spender.  When the presigned transactions are confirmed
      to a suitable depth, the receiver can be certain they have
      trustless control over the funds.  However, they lose the benefits
      of being part of a joinpool, such as rapid settlement and lower
      fees deriving from UTXO sharing.  They may also need to pay
      transaction fees to confirm the chain of transactions.

    - *Nothing:* in the normal case, a presigned transaction in the chain
      of transactions will eventually expire and allow the server to claim
      the funds.  This is not theft---it's an expected part of the
      protocol---and the server may choose to return some or all of the
      claimed funds to the user in some way.  Until the expiry approaches,
      the receiver can just wait.

      In the pathological case, the server and spender can (at any time)
      collude to sign an alternative chain of transactions to steal the
      funds sent to the receiver.  Note: Bitcoin's privacy properties
      allow both the server and the spender to be the same person, so
      collusion might not even be required.  However, if the receiver
      keeps a copy of the chain of transactions cosigned by the server,
      they can prove that the server stole funds, which might be
      sufficient to deter other people from using that server.

    - *Refresh:* with server cooperation, the receiver can atomically
      transfer ownership over the funds in the spender-cosigned
      transaction for another presigned transaction with the receiver as
      a cosigner.  This extends the expiration date and eliminates the
      ability for the server and previous spender to collude to steal the
      previously sent funds.  However, refreshing requires that the server
      hold on to the refreshed funds until they expire, reducing the
      server's liquidity, so the server charges the receiver an interest
      rate until expiration (paid upfront since the expiration time is
      fixed).

    Another design goal for Ark is to allow participants to receive LN
    payments.  In his original post and a [reply][roose ctv-ark-ln],
    Roose describes that existing participants who already have funds
    inside the joinpool can be penalized up to the cost of an onchain
    transaction if they fail to perform the interactivity required for
    receiving an LN payment.  However, those who don't already have
    funds in the joinpool can't be penalized, so they can refuse to
    perform the interactive steps and costlessly create problems for
    honest participants.  This appears to effectively prevent Ark users
    from receiving LN payments unless they already have a moderate
    amount of funds on deposit with the Ark server they want to use.

    Roose then describes how the availability of CTV would allow
    the protocol to be improved.  The main change is how Ark rounds are
    created.  An _Ark round_ consists of a small onchain transaction that
    commits to a tree of offchain transactions.  These are presigned
    transactions in the case of clArk, requiring all of the spenders in
    that round to be available for signing.  If CTV was available, each
    branch in the tree of transactions can commit to its descendants using
    CTV with no signing required.  This allows the creation of
    transactions even for participants who aren't available at the time
    the round is created, with the following benefits:

    - *In-round non-interactive payments:* instead of Ark out-of-round
      (arkoor) payments, a spender who is willing to wait for the next
      round can pay the receiver in-round.  For the receiver, this has a
      major advantage: as soon as the round confirms to a suitable depth,
      they receive trustless control over their received funds (until
      expiration approaches, at which time they can either exit or
      cheaply refresh).  Instead of waiting for several confirmations,
      the receiver can choose to immediately trust the incentives
      created by the Ark protocol for the server to operate honestly
      (see [Newsletter #253][news253 ark 0conf]).  In a separate point,
      Roose notes that these non-interactive payments can also be
      [batched][topic payment batching] to pay multiple receivers at
      once.

    - *In-round acceptance of LN payments:* a user could request an LN
      payment ([HTLC][topic htlc]) be sent to an Ark server, the server
      would then [hold][topic hold invoices] the payment until its next
      round, and it would use CTV to include an HTLC-locked payment to the
      user in the round---after which the user could disclose the HTLC
      preimage to claim the payment.  However, Roose noted that this
      would still require the use of "various anti-abuse measures" (we
      believe that this is because of the risk of the receiver failing
      to disclose the preimage, leading to the server's funds remaining
      locked until the end of the Ark round, which might extend for two
      or more months).

      David Harding [replied][harding ctv-ark-ln] to Roose asking for
      additional details and comparing the situation to LN [JIT
      channels][topic jit channels], which have a similar problem with
      non-revelation of HTLC preimages creating problems for Lightning
      Service Providers (LSPs).  LSPs currently address that problem
      through the introduction of trust-based mechanisms (see
      [Newsletter #256][news256 ln-jit]).  If the same solutions were
      planned to be used with CTV-Ark, those solutions would also seem
      to safely allow in-round acceptance of LN payments in clArk.

    - *Fewer rounds, fewer signatures, and less storage:* clArk uses
      [musig2][topic musig], and each party needs to participate in
      multiple rounds, generate multiple partial signatures, and store
      completed signatures.  With CTV, less data would need to be
      generated and stored and less interaction would be required.

- **OP_CHECKCONTRACTVERIFY semantics:** Salvatore Ingala
  [posted][ingala ccv] to Delving Bitcoin to describe the semantics of
  the proposed [OP_CHECKCONTRACTVERIFY][topic matt] (CCV) opcode, link
  to a [first draft BIP][ccv bip], and link to a [implementation
  draft][bitcoin core #32080] for Bitcoin Core.  His description starts
  with an overview of CCV's behavior: it allows checking that a public
  key commits to an arbitrary piece of data.  It can check both the
  public key of the [taproot][topic taproot] output being spent or the
  public key of a taproot output being created.  This can be used to
  ensure that data from the output being spent is carried over to the
  output being created.  In taproot, a tweak of the output can commit to
  tapleaves such [tapscripts][topic tapscript].  If the tweak commits to
  one or more tapscripts, it places an _encumbrance_ (spending
  condition) on the output, allowing conditions placed on the output
  being spent to be transferred to the output being created---commonly
  (but [controversially][towns anticov]) called a [covenant][topic
  covenants] in Bitcoin jargon.  The covenant may allow satisfaction or
  modification of the encumbrance, which would (respectively) terminate
  the covenant or modify its terms for future iterations.  Ingala
  describes some of the benefits and drawbacks of this approach:

  - *Benefits:* taproot native, doesn't increase the size of taproot
    entries in the UTXO set, and spending paths that don't require the
    extra data don't need to include it in their witness stack (so
    there's no extra cost in that case).

  - *Drawbacks:* only works with taproot and checking tweaks requires
    elliptical curve operations that are more expensive than (say)
    SHA256 operations.

  Only transferring the spending conditions from the output being spent
  to an output being can be useful, but many covenants will want to
  ensure some or all of the bitcoins in the output being spent are
  passed through to the output being created.  Ingala describes CCV's
  three options for handling values.

  - *Ignore:* don't check amounts.

  - *Deduct:* the amount of an output being created at a particular
    index (e.g., the third output) is deducted from the amount of the
    output being spent at the same index and the residual value is
    tracked.  For example, if the output being spent at index three is
    worth 100 BTC and the output being created at index three is worth
    70 BTC, then the code keeps track of the residual 30 BTC.  The
    transaction is marked as invalid if the output being created is
    greater than the output being spent (as that would reduce the
    residual value, perhaps below zero).

  - *Default:* mark the transaction invalid unless the amount of output
    being created at a particular index is greater than the amount of
    the output being spent plus the sum of any previous residuals that
    haven't been used with a _default_ check yet.

  A transaction is valid if any output is checked more than once with
  _deduct_ or if both _deduct_ and _default_ are used on the same
  output.

  Ingala provides several visual examples of combinations of the above
  operations.  Here's our textual description of his "send partial
  amount" example, which might be useful for a [vault][topic vaults]: a
  transaction has one input (spending one output) worth 100 BTC and two
  outputs, one for 70 BTC and the other for 30 BTC.  CCV is run twice
  during validation of the input:

  1. CCV with _deduct_ operates index 0 for 100 BTC spent to 70 BTC
     created, giving a residual of 30.  In a [BIP345][]-style vault, CCV
     would be returning that 70 BTC back to the same script it was
     previously secured by.

  2. The second time, it uses _default_ on index 1.  Although there's an
     output being created at index 1 in this transaction, there's no
     corresponding output being spent at index 1, so the implicit amount
     `0` is used.  To that zero is added the residual 30 BTC from the
     _deduct_ call on index 0, requiring this output being created equal
     30 BTC or greater.  In a BIP345-style vault, CCV would tweak the
     output script to allow spending this value to an arbitrary address
     after a [timelock][topic timelocks] expires or returning it to the
     user's main vault address at any time.

  Several alternative approaches that Ingala considered and discarded
  are discussed both in his post and the replies.  He writes, "I think
  the two amount behaviors (default and deduct) are very ergonomic, and
  cover the vast majority of the desirable amount checks in practice."

  He also notes that he "implemented fully featured vaults using
  `OP_CCV` plus [OP_CTV][topic op_checktemplateverify] that are roughly
  equivalent to [...[BIP345][]...]  Moreover, a reduced-functionality
  version using just `OP_CCV` is implemented as a functional test in the
  Bitcoin Core implementation of `OP_CCV`."

- **Draft BIP published for consensus cleanup:** Antoine Poinsot
  [posted][poinsot cleanup] to the Bitcoin-Dev mailing list a link to a
  [draft BIP][cleanup bip] he's written for the [consensus cleanup][topic
  consensus cleanup] soft fork proposal.  It includes several fixes:

  - Fixes for two different [time warp][topic time warp] attacks that
    could be used by a majority of hashrate to produce blocks at an
    accelerated rate.

  - A signature operations (sigops) execution limit on legacy
    transactions to prevent the creation of excessively slow-to-validate
    blocks.

  - A fix for [BIP34][] coinbase transaction uniqueness that should
    fully prevent [duplicate transactions][topic duplicate transactions].

  - Invalidation of future 64-byte transaction (calculated using
    stripped size) to prevent a type of [merkle tree
    vulnerability][topic merkle tree vulnerabilities].

  Technical replies were favorable for all but two parts of the
  proposal.  The first objection, made in several replies, was to
  invalidation of 64-byte transactions.  The replies restated previous
  criticism (see [Newsletter #319][news319 64byte]).  An alternative
  method for addressing merkle tree vulnerabilities exists.  That method
  is relatively easy for lightweight (SPV) wallets to use but might
  present challenges to SPV validation in smart contracts, such as
  _bridges_ between Bitcoin and other systems.  Sjors Provoost
  [suggested][provoost bridge] someone implementing an
  onchain-enforcable bridge provide code illustrating the difference
  between being able to assume 64 byte transactions don't exist and
  having to use the alternative method for preventing merkle tree
  vulnerabilities.

  The second objection was about a late change to the ideas include in
  the BIP, which was described in a [post][poinsot nsequence] on Delving
  Bitcoin by Poinsot.  The change is requiring blocks made after
  activation of consensus cleanup to set the flag that ensures makes
  their locktime enforcable.  As previously proposed, coinbase
  transactions in post-activation blocks will set their locktime to
  their block height (minus 1).  This change means no miner can produce
  an alternative early Bitcoin block that uses both post-activation
  locktime and sets the enforcement flag (because, if they did, their
  coinbase transaction wouldn't be valid in the block that included it
  due to its use of a far-future locktime).   The inability to use
  exactly the same values in a past coinbase transaction as will be used
  in a future coinbase transaction prevents the duplicate transactions
  vulnerability.  The objection to this proposal was that it wasn't
  clear whether all miners are capable of setting the locktime
  enforcement flag.

## Releases and release candidates

_New releases and release candidates for popular Bitcoin infrastructure
projects.  Please consider upgrading to new releases or helping to test
release candidates._

- [Bitcoin Core 29.0rc3][] is a release candidate for the next major
  version of the network's predominate full node.  Please see the
  [version 29 testing guide][bcc29 testing guide].

- [LND 0.19.0-beta.rc1][] is a release candidate for this popular LN
  node.  One of the major improvements that could probably use testing
  is the new RBF-based fee bumping for cooperative closes described
  below in the notable code changes section.

## Notable code and documentation changes

_Notable recent changes in [Bitcoin Core][bitcoin core repo], [Core
Lightning][core lightning repo], [Eclair][eclair repo], [LDK][ldk repo],
[LND][lnd repo], [libsecp256k1][libsecp256k1 repo], [Hardware Wallet
Interface (HWI)][hwi repo], [Rust Bitcoin][rust bitcoin repo], [BTCPay
Server][btcpay server repo], [BDK][bdk repo], [Bitcoin Improvement
Proposals (BIPs)][bips repo], [Lightning BOLTs][bolts repo],
[Lightning BLIPs][blips repo], [Bitcoin Inquisition][bitcoin inquisition
repo], and [BINANAs][binana repo]._

- [Bitcoin Core #31363][] introduces the `TxGraph` class, a lightweight
  in-memory model of mempool transactions that tracks only feerates and
  dependencies between transactions. It includes mutation functions such as
  `AddTransaction`, `RemoveTransaction`, and `AddDependency`, and inspection
  functions such as `GetAncestors`, `GetCluster`, and `CountDistinctClusters`.
  `TxGraph` also supports staging of changes with commit and abort
  functionality. This is part of the [cluster mempool][topic cluster mempool]
  project, and prepares for future improvements to mempool eviction,
  reorganization handling, and cluster-aware mining logic.

- [Bitcoin Core #31278][] deprecates the `settxfee` RPC command and the
  `-paytxfee` startup option, which allow users to set a static fee rate for all
  transactions. Users should instead rely on fee estimation or set a
  per-transaction fee rate. They are marked for removal in Bitcoin Core 31.0.

- [Eclair #3050][] updates how [BOLT12][topic offers] payment failures are
  relayed when the recipient is a directly connected node, to always forward the
  failure message instead of overriding it with an unreadable
  `invalidOnionBlinding` failure. If the failure includes a `channel_update`,
  Eclair overrides it with `TemporaryNodeFailure` to avoid revealing details
  about [unannounced channels][topic unannounced channels]. For [blinded
  routes][topic rv routing] involving other nodes, Eclair continues to override
  failures with `invalidOnionBlinding`. All failure messages are encrypted using
  the wallet’s `blinded_node_id`.

- [Eclair #2963][] implements 1p1c [package relay][topic package relay] by
  calling Bitcoin Core’s `submitpackage` RPC command during channel force
  closures to broadcast both the commitment transaction and its anchor together.
  This allows commitment transactions to propagate even if their feerate is
  below the mempool minimum, but requires connecting to peers running Bitcoin
  Core 28.0 or later. This change removes the need to dynamically set the
  feerate of commitment transactions, and ensures that force closures don’t get
  stuck when nodes disagree on the current feerate.

- [Eclair #3045][] makes the `payment_secret` field in the outer onion payload
  optional for single-part [trampoline payments][topic trampoline payments].
  Previously, every trampoline payment included a `payment_secret`, even if
  [multipath payments][topic multipath payments] (MPP) wasn't used. Since
  payment secrets are required when handling BOLT11 invoices, Eclair inserts a
  dummy one on decryption if one isn’t provided.

- [LDK #3670][] adds support for handling and receiving [trampoline
  payments][topic trampoline payments], but does not yet implement providing a
  trampoline routing service. This is a prerequisite for a type of [async
  payments][topic async payments] that LDK plans to deploy.

- [LND #9620][] adds [testnet4][topic testnet] support by adding the necessary
  parameters and blockchain constants such as its genesis hash.

{% include snippets/recap-ad.md when="2025-04-08 15:30" %}
{% include references.md %}
{% include linkers/issues.md v=2 issues="31363,31278,3050,2963,3045,3670,9620,32080" %}
[bitcoin core 29.0rc3]: https://bitcoincore.org/bin/bitcoin-core-29.0/
[bcc29 testing guide]: https://github.com/bitcoin-core/bitcoin-devwiki/wiki/29.0-Release-Candidate-Testing-Guide
[lnd 0.19.0-beta.rc1]: https://github.com/lightningnetwork/lnd/releases/tag/v0.19.0-beta.rc1
[back crsig]: https://bitcointalk.org/index.php?topic=206303.msg2162962#msg2162962
[bip119 prechange]: https://github.com/bitcoin/bips/blob/9573e060e32f10446b6a2064a38bdc2047171d9c/bip-0119.mediawiki
[news75 ctv]: /en/newsletters/2019/12/04/#op-checktemplateverify-ctv
[news190 recursive]: /en/newsletters/2022/03/09/#limiting-script-language-expressiveness
[modern ctv]: /en/newsletters/2019/12/18/#proposed-changes-to-bip-ctv
[rubin enumeration]: https://gnusha.org/pi/bitcoindev/CAD5xwhjj3JAXwnrgVe_7RKx0AVDDy4X-L9oOnwhswXAQFoJ7Bw@mail.gmail.com/
[towns ctvmot]: https://mailing-list.bitcoindevs.xyz/bitcoindev/Z8eUQCfCWjdivIzn@erisian.com.au/
[mn recursive]: https://mutinynet.com/address/tb1p0p5027shf4gm79c4qx8pmafcsg2lf5jd33tznmyjejrmqqx525gsk5nr58
[rubin recurse]: https://gnusha.org/pi/bitcoindev/CAD5xwhjsVA7k7ZQ_QdrcZOxdi+L6L7dvqAj1Mhx+zmBA3DM5zw@mail.gmail.com/
[osuntokun enum]: https://mailing-list.bitcoindevs.xyz/bitcoindev/CAO3Pvs-1H2s5Dso0z5CjKcHcPvQjG6PMMXvgkzLwXgCHWxNV_Q@mail.gmail.com/
[towns enum]: https://mailing-list.bitcoindevs.xyz/bitcoindev/Z8tes4tXo53_DRpU@erisian.com.au/
[ctv spookchain]: https://github.com/bitcoin/bips/pull/1792/files#diff-aaa82c3decf53fb4312de88fbb3cc081da786b72387c9fec7bfb977ad3558b91R589-R593
[ivgi minsc]: https://mailing-list.bitcoindevs.xyz/bitcoindev/CAGXD5f3EGyUVBc=bDoNi_nXcKmW7M_-mUZ7LOeyCCab5Nqt69Q@mail.gmail.com/
[minsc]: https://min.sc/
[poinsot alt]: https://mailing-list.bitcoindevs.xyz/bitcoindev/1JkExwyWEPJ9wACzdWqiu5cQ5WVj33ex2XHa1J9Uyew-YF6CLppDrcu3Vogl54JUi1OBExtDnLoQhC6TYDH_73wmoxi1w2CwPoiNn2AcGeo=@protonmail.com/
[moonsettler express]: https://mailing-list.bitcoindevs.xyz/bitcoindev/Gfgs0GeY513WBZ1FueJBVhdl2D-8QD2NqlBaP0RFGErYbHLE-dnFBN_rbxnTwzlolzpjlx0vo9YSgZpC013Lj4SI_WZR0N1iwbUiNze00tA=@protonmail.com/
[obeirne readiness]: https://mailing-list.bitcoindevs.xyz/bitcoindev/45ce340a-e5c9-4ce2-8ddc-5abfda3b1904n@googlegroups.com/
[nick dlc]: https://gist.github.com/jonasnick/e9627f56d04732ca83e94d448d4b5a51#dlcs
[lava 30m]: https://x.com/MarediaShehzan/status/1896593917631680835
[news322 csv-bitvm]: /en/newsletters/2024/09/27/#shielded-client-side-validation-csv
[news253 ark 0conf]: /en/newsletters/2023/05/31/#making-internal-transfers
[clark doc]: https://ark-protocol.org/intro/clark/index.html
[oor doc]: https://ark-protocol.org/intro/oor/index.html
[lopp destroy]: https://mailing-list.bitcoindevs.xyz/bitcoindev/CADL_X_cF=UKVa7CitXReMq8nA_4RadCF==kU4YG+0GYN97P6hQ@mail.gmail.com/
[boris timelock]: https://mailing-list.bitcoindevs.xyz/bitcoindev/CAFC_Vt54W1RR6GJSSg1tVsLi1=YHCQYiTNLxMj+vypMtTHcUBQ@mail.gmail.com/
[habovstiak gfsig]: https://mailing-list.bitcoindevs.xyz/bitcoindev/CALkkCJY=dv6cZ_HoUNQybF4-byGOjME3Jt2DRr20yZqMmdJUnQ@mail.gmail.com/
[news141 gfsig]: /en/newsletters/2021/03/24/#taproot-improvement-in-post-qc-recovery-at-no-onchain-cost
[guy fawkes signature scheme]: https://www.cl.cam.ac.uk/archive/rja14/Papers/fawkes.pdf
[fournier gfsig]: https://mailing-list.bitcoindevs.xyz/bitcoindev/CALkkCJYaLMciqYxNFa6qT6-WCsSD3P9pP7boYs=k0htAdnAR6g@mail.gmail.com/T/#ma2a9878dd4c63b520dc4f15cd51e51d31d323071
[wuille nonpublic]: https://mailing-list.bitcoindevs.xyz/bitcoindev/pXZj0cBHqBVPjkNPKBjiNE1BjPHhvRp-MwPaBsQu-s6RTEL9oBJearqZE33A2yz31LNRNUpZstq_q8YMN1VsCY2vByc9w4QyTOmIRCE3BFM=@wuille.net/T/#mfced9da4df93e56900a8e591d01d3b3abfa706ed
[cruz qramp]: https://mailing-list.bitcoindevs.xyz/bitcoindev/08a544fa-a29b-45c2-8303-8c5bde8598e7n@googlegroups.com/
[news347 bip119]: /en/newsletters/2025/03/28/#bips-1792
[roose ctvcsfs]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/
[poinsot ctvcsfs1]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/4
[10101 shutdown]: https://10101.finance/blog/10101-is-shutting-down
[towns vaults]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/14
[obeirne ctv-vaults]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/23
[sanders bitvm]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/7
[obeirne liquid]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/24
[kanjalkar liquid]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/28
[poelstra liquid]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/32
[news284 lnsym]: /en/newsletters/2024/01/10/#ln-symmetry-research-implementation
[sanders versus]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/7
[towns nonrepo]: https://delvingbitcoin.org/t/ctv-csfs-can-we-reach-consensus-on-a-first-step-towards-covenants/1509/14
[roose ctv-ark]: https://codeberg.org/ark-bitcoin/bark/src/branch/ctv
[roose ctv-for-ark]: https://delvingbitcoin.org/t/the-ark-case-for-ctv/1528/
[roose ctv-ark-ln]: https://delvingbitcoin.org/t/the-ark-case-for-ctv/1528/5
[harding ctv-ark-ln]: https://delvingbitcoin.org/t/the-ark-case-for-ctv/1528/8
[news256 ln-jit]: /en/newsletters/2023/06/21/#just-in-time-jit-channels
[ruffing gfsig]: https://gnusha.org/pi/bitcoindev/1518710367.3550.111.camel@mmci.uni-saarland.de/
[cruz bip]: https://github.com/chucrut/bips/blob/master/bip-xxxxx.md
[towns anticov]: https://gnusha.org/pi/bitcoindev/20220719044458.GA26986@erisian.com.au/
[ccv bip]: https://github.com/bitcoin/bips/pull/1793
[ingala ccv]: https://delvingbitcoin.org/t/op-checkcontractverify-and-its-amount-semantic/1527/
[news319 64byte]: /en/newsletters/2024/09/06/#mitigating-merkle-tree-vulnerabilities
[poinsot nsequence]: https://delvingbitcoin.org/t/great-consensus-cleanup-revival/710/79
[provoost bridge]: https://mailing-list.bitcoindevs.xyz/bitcoindev/19f6a854-674a-4e4d-9497-363af306e3a0@app.fastmail.com/
[poinsot cleanup]: https://mailing-list.bitcoindevs.xyz/bitcoindev/uDAujRxk4oWnEGYX9lBD3e0V7a4V4Pd-c4-2QVybSZNcfJj5a6IbO6fCM_xEQEpBvQeOT8eIi1r91iKFIveeLIxfNMzDys77HUcbl7Zne4g=@protonmail.com/
[cleanup bip]: https://github.com/darosior/bips/blob/consensus_cleanup/bip-cc.md
[news312 chilldkg]: /en/newsletters/2024/07/19/#distributed-key-generation-protocol-for-frost
[fnr secp]: https://mailing-list.bitcoindevs.xyz/bitcoindev/d0044f9c-d974-43ca-9891-64bb60a90f1f@gmail.com/
[secp256k1lab]: https://github.com/secp256k1lab/secp256k1lab
[corallo eltoo]: https://x.com/TheBlueMatt/status/1857119394104500484
