# Kusama Parathread Registration

## Manual Test Protocol

### Block Production and Runtime Upgrade
1. Edit and launch: `launch-rococo-local-with-shell.json`
    * add path to `pokladot-0-9.3-local-testing`
    * remove the parachains
    * run the edited file.
2. Reserve ParaIds up to 2015
3. Register Parathread with id 2015 and wait two sessions until it is onboarded:
   * clangenb needs to use these files for registration:
      * shell-rococo-local-dev.state
      * shell-rococo-local-dev.wasm
   * brenzi these, as they contain integritee-keys:
      * shell-rococo-local.state
      * shell-rococo-local.wasm
   
4. Run integritee-collators. Note, we explicitly set the db, although it should not be necessary.
* clangenb, two collators for both dev authorities:
```shell
 ./integritee-collator --dave --collator -d /tmp/dave/integritee_test --parachain-id 2015 --chain shell-rococo-local-dev-spec.json --port 40335 --ws-port 9946 -- --execution wasm --chain ../integritee-parachain/rococo-local-raw.json --port 30337 --ws-port 9981
 ./integritee-collator --eve --collator -d /tmp/eve/integritee_test --parachain-id 2015 --chain shell-rococo-local-dev-spec.json --port 40336 --ws-port 9947 -- --execution wasm --chain ../integritee-parachain/rococo-local-raw.json --port 30338 --ws-port 9982
```

* brenzi: collators for some initial authorities (this command is untested):
```shell
  ./integritee-collator --collator -d <define-db-as-above> --node-key-file <aura_authority_key_file> --parachain-id 2015 --chain shell-rococo-local-spec.json --port 40335 --ws-port 9946 -- --execution wasm --chain --chain rococo-local-raw.json --port 30337 --ws-port 9981
```

**Caveat:** `rococo-local.json` needs to be generated by polkadot-launch because it updates the original spec. Hence,
if a plain spec produced by the build-spec command is used, the genesis is different.

5. Upgrade parathread to parachain via `sudo.call(parasSudoWrapper.sudoScheduleParathreadUpdgrade(2015))`
6. Verify:
    * Wait until the parachains are upgraded and produce blocks.
    * Make a transfer and check that fees are applied.
    * Make a `vesting.vestedTransfer` and check that vesting works.
9. Perform a runtime upgrade with:
   * clangenb: `sudo.sudoUncheckedWeight(system.setCode(integritee-rococo-local-dev.wasm))`
   * brenzi: `sudo.sudoUncheckedWeight(system.setCode(integritee-rococo-local.wasm))`
10. Verify:
    * That the new pallets are included
    * Make a transfer and check that fees are applied.
    * Make a `vesting.vestedTransfer` and check that vesting works.
11. By now, we are still running the old client, which implies that the chain name is still the same. Hence, we restart the chain and pass not the
new chain-spec.
* clangenb: 
```shell
 ./integritee-collator --dave --collator -d /tmp/dave/integritee_test --parachain-id 2015 --chain integritee-rococo-local-dev-spec.json --port 40335 --ws-port 9946 -- --execution wasm --chain ../integritee-parachain/rococo-local-raw.json --port 30337 --ws-port 9981
```

* brenzi (this command is untested):
```shell
  ./integritee-collator --collator -d <define-db-as-above> --node-key-file <aura_authority_key_file> --parachain-id 2015 --chain integritee-rococo-local-spec.json --port 40335 --ws-port 9946 -- --execution wasm --chain --chain rococo-local-raw.json --port 30337 --ws-port 9981
```

12. Verify:
    * That we continue to build blocks on the same chain.
    * That the new chain name, i.e. `Integritee Network`, is displayed in polkadot-js/app.
    
    
The above procedure has been verified by clangenb with the following artifacts:
* shell-rococo-local-dev-spec.json: https://github.com/scs/integritee-parachain/suites/3030614115/artifacts/68761361
* shell-rococo-local-dev.state: https://github.com/scs/integritee-parachain/suites/3030614115/artifacts/68761359
* shell-rococo-local-dev.wasm: https://github.com/scs/integritee-parachain/suites/3030614115/artifacts/68761360
* integritee-rococo-local-dev.wasm: https://github.com/scs/integritee-parachain/suites/3030614115/artifacts/68761350
* integritee-rococo-local-dev-spec.json: https://github.com/scs/integritee-parachain/suites/3030614115/artifacts/68761351

### Verification of keys and token allocation

Verify that:
* Integritee Sudo Address: 5EqGFRTN3m2kLpoaThANra5REs5C7B2rfLmmZv2nbJsxaTe1
* Root has 10MTEER
* Total Supply is 10MTEER
* ParaId is 2015 (clangenb is not sure if he can test that without authority keys. But he successfully registered the parathread.)

Key and Resource Allocation has been verified by clangenb with the following artifacts:
* shell-rococo-local-spec.json: https://github.com/scs/integritee-parachain/suites/3030614115/artifacts/68761362
* shell-rococo-local.state: https://github.com/scs/integritee-parachain/suites/3030614115/artifacts/68761356
* shell-rococo-local.wasm: https://github.com/scs/integritee-parachain/suites/3030614115/artifacts/68761358

For all tests the following integritee-collator was used: https://github.com/scs/integritee-parachain/suites/3030614115/artifacts/68761343