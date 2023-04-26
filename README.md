Capstone project for Daml Fundamentals certification
----------------------------------------------------

Applicant: Cale Gibbard (cale.gibbard@obsidian.systems)

This project overall consists of Daml contracts for a private NFT that allows users to
split, merge, and trade subintervals of [0,1] with rational endpoints.

* Rational.daml contains a quick implementation of rational numbers, since those had ironically
  been deleted from the standard libraries relative to Haskell, with instances of many of
  Daml's numerical type classes (I left some out, because I didn't even really need all of what I
  wrote).

* UserAdmin.daml contains contracts for users to sign up with a user admin either as potential
  owners of intervals, or as payment authorities (who are meant to report success/failure status
  on payment transfers, after agreeing to mediate).

* Interval.daml contains the main Interval contract which consists of a lower bound (inf),
  an upper bound (sup), an owner Party, and a userAdmin Party. It has the following choices on it:
  - Interval_Burn : simply consumes the contract
  - Interval_Split : splits the interval at a given rational pt which must fall strictly inside it,
      creating two new Interval contracts
  - Interval_Merge : merges an interval with an interval to its right (whose inf matches its sup),
      consuming both, but producing a new joined interval
  - Interval_OfferRequest : requests a given payment authority approve the transfer of the interval
      to a given party, for a given (textually described) price. This results in an
      IntervalOfferRequest which the payment authority can approve or decline based on its judgment 
      about whether it will be able to confirm payment, and if it is approved
      that results in an IntervalOffer which can be accepted or declined by the counterparty,
      and if that is further accepted, it results in an IntervalPendingPayment which
      the payment authority then marks as either suceessful (in the case that the payment went
      through), resulting in a transfer to the new owner, or failed, which results in transfer back
      to the original owner.

* Main.daml contains a commentated test script which sets up a scenario with Alice and Bob as
  (potential) owners of intervals, Percival a payment authority, and UserAdmin, an administrator
  who approves Alice, Bob and Percival's roles, and then gives the entire interval initially to
  Alice, who then splits it, sells a piece to Bob, who splits it again, and sells a piece back
  to Alice, which she re-merges with her original piece.

* Util.daml contains a utility function.
