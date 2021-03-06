# Migration `20200906101600-add-token-registry`

This migration has been generated by Wanseob Lim at 9/6/2020, 10:16:00 AM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TABLE "public"."EncryptedWallet" (
"N" integer  NOT NULL ,"algorithm" text  NOT NULL ,"ciphertext" text  NOT NULL ,"id" text  NOT NULL ,"iv" text  NOT NULL ,"kdf" text  NOT NULL ,"keylen" integer  NOT NULL ,"p" integer  NOT NULL ,"r" integer  NOT NULL ,"salt" text  NOT NULL ,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."Keystore" (
"address" text  NOT NULL ,"encrypted" text  NOT NULL ,"zkAddress" text  NOT NULL ,
    PRIMARY KEY ("address"))

CREATE TABLE "public"."Config" (
"address" text  NOT NULL ,"chainId" integer  NOT NULL ,"challengePeriod" integer  NOT NULL ,"id" text  NOT NULL ,"maxUtxo" text  NOT NULL ,"maxWithdrawal" text  NOT NULL ,"minimumStake" text  NOT NULL ,"networkId" integer  NOT NULL ,"nullifierTreeDepth" integer  NOT NULL ,"referenceDepth" integer  NOT NULL ,"utxoSubTreeDepth" integer  NOT NULL ,"utxoSubTreeSize" integer  NOT NULL ,"utxoTreeDepth" integer  NOT NULL ,"withdrawalSubTreeDepth" integer  NOT NULL ,"withdrawalSubTreeSize" integer  NOT NULL ,"withdrawalTreeDepth" integer  NOT NULL ,
    PRIMARY KEY ("networkId","chainId","address"))

CREATE TABLE "public"."Proposal" (
"fetched" text   ,"finalized" boolean   ,"hash" text  NOT NULL ,"isUncle" boolean   ,"proposalData" text   ,"proposalNum" integer   ,"proposalTx" text   ,"proposedAt" integer   ,"verified" boolean   ,
    PRIMARY KEY ("hash"))

CREATE TABLE "public"."Block" (
"hash" text  NOT NULL ,
    PRIMARY KEY ("hash"))

CREATE TABLE "public"."Header" (
"depositRoot" text  NOT NULL ,"fee" text  NOT NULL ,"hash" text  NOT NULL ,"metadata" text  NOT NULL ,"migrationRoot" text  NOT NULL ,"nullifierRoot" text  NOT NULL ,"parentBlock" text  NOT NULL ,"proposer" text  NOT NULL ,"txRoot" text  NOT NULL ,"utxoIndex" text  NOT NULL ,"utxoRoot" text  NOT NULL ,"withdrawalIndex" text  NOT NULL ,"withdrawalRoot" text  NOT NULL ,
    PRIMARY KEY ("hash"))

CREATE TABLE "public"."Bootstrap" (
"blockHash" text   ,"id" text  NOT NULL ,"utxoBootstrap" text  NOT NULL ,"withdrawalBootstrap" text  NOT NULL ,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."MassDeposit" (
"blockNumber" integer  NOT NULL ,"fee" text  NOT NULL ,"includedIn" text   ,"index" text  NOT NULL ,"merged" text  NOT NULL ,
    PRIMARY KEY ("index"))

CREATE TABLE "public"."Deposit" (
"blockNumber" integer  NOT NULL ,"fee" text  NOT NULL ,"logIndex" integer  NOT NULL ,"note" text  NOT NULL ,"queuedAt" text  NOT NULL ,"transactionIndex" integer  NOT NULL ,
    PRIMARY KEY ("note"))

CREATE TABLE "public"."Utxo" (
"erc20Amount" text   ,"eth" text   ,"hash" text  NOT NULL ,"index" text   ,"nft" text   ,"nullifier" text   ,"owner" text   ,"salt" text   ,"status" integer   ,"tokenAddr" text   ,"treeId" text   ,"usedAt" text   ,
    PRIMARY KEY ("hash"))

CREATE TABLE "public"."Withdrawal" (
"erc20Amount" text  NOT NULL ,"eth" text  NOT NULL ,"fee" text  NOT NULL ,"hash" text  NOT NULL ,"includedIn" text   ,"index" text   ,"nft" text  NOT NULL ,"owner" text   ,"prepayer" text   ,"salt" text   ,"siblings" text   ,"status" integer   ,"to" text  NOT NULL ,"tokenAddr" text  NOT NULL ,"treeId" text   ,"withdrawalHash" text  NOT NULL ,
    PRIMARY KEY ("hash"))

CREATE TABLE "public"."Migration" (
"erc20Amount" text   ,"eth" text   ,"fee" text   ,"hash" text  NOT NULL ,"index" text   ,"nft" text   ,"owner" text   ,"salt" text   ,"status" integer   ,"to" text   ,"tokenAddr" text   ,"treeId" text   ,"usedFor" text   ,
    PRIMARY KEY ("hash"))

CREATE TABLE "public"."TreeNode" (
"nodeIndex" text  NOT NULL ,"treeId" text  NOT NULL ,"value" text  NOT NULL ,
    PRIMARY KEY ("treeId","nodeIndex"))

CREATE TABLE "public"."LightTree" (
"end" text  NOT NULL ,"id" text  NOT NULL ,"index" text  NOT NULL ,"root" text  NOT NULL ,"siblings" text  NOT NULL ,"species" integer  NOT NULL ,"start" text  NOT NULL )

CREATE TABLE "public"."TokenRegistry" (
"address" text  NOT NULL ,"blockNumber" integer  NOT NULL ,"identifier" integer  NOT NULL ,"isERC20" boolean  NOT NULL ,"isERC721" boolean  NOT NULL ,
    PRIMARY KEY ("address"))

CREATE UNIQUE INDEX "Config.id" ON "public"."Config"("id")

CREATE UNIQUE INDEX "Proposal_fetched" ON "public"."Proposal"("fetched")

CREATE UNIQUE INDEX "Bootstrap.blockHash" ON "public"."Bootstrap"("blockHash")

CREATE UNIQUE INDEX "LightTree.id" ON "public"."LightTree"("id")

CREATE UNIQUE INDEX "LightTree.species" ON "public"."LightTree"("species")

ALTER TABLE "public"."Proposal" ADD FOREIGN KEY ("fetched")REFERENCES "public"."Block"("hash") ON DELETE SET NULL  ON UPDATE CASCADE

ALTER TABLE "public"."Block" ADD FOREIGN KEY ("hash")REFERENCES "public"."Header"("hash") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Bootstrap" ADD FOREIGN KEY ("blockHash")REFERENCES "public"."Block"("hash") ON DELETE SET NULL  ON UPDATE CASCADE

ALTER TABLE "public"."Utxo" ADD FOREIGN KEY ("treeId")REFERENCES "public"."LightTree"("id") ON DELETE SET NULL  ON UPDATE CASCADE

ALTER TABLE "public"."Withdrawal" ADD FOREIGN KEY ("treeId")REFERENCES "public"."LightTree"("id") ON DELETE SET NULL  ON UPDATE CASCADE

ALTER TABLE "public"."Migration" ADD FOREIGN KEY ("treeId")REFERENCES "public"."LightTree"("id") ON DELETE SET NULL  ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration 20200817060942-4..20200906101600-add-token-registry
--- datamodel.dml
+++ datamodel.dml
@@ -5,9 +5,9 @@
 }
 datasource postgres {
   provider = "postgres"
-  url = "***"
+  url = "***"
 }
 model EncryptedWallet {
   id String @id @default(uuid())
@@ -182,4 +182,12 @@
   root String
   index String
   siblings String // stringified str[]
 }
+
+model TokenRegistry {
+  address String @id
+  isERC20 Boolean
+  isERC721 Boolean
+  identifier Int
+  blockNumber Int
+}
```
