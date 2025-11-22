<h1 align="center">Citrea Trusted Setup Verification</h1>

 Citrea Risc0-to-BitVM Trusted Setup Ceremony has been completed successfully! This is the first trusted setup for ZK proofs on Bitcoin for BitVM. Now Clementine and other BitVM projects can use shared and verifiable cryptographic parameters. A total of 64 people contributed, and all Clementine bridge signers were included. No extra trust assumptions were introduced.
 
 > For more information [click.](https://dev.risczero.com/api/trusted-setup-ceremony)

The following verification is important to ensure that the setup was not tampered with and that Clementine is using the exactly generated parameters. By recompiling the circuit, downloading the correct Powers of Tau and final zkey, and checking the transcript, you can regenerate the commitments and compare them with the published parameters and verification key hashes.

NOTE: In simpler terms; this is not a contribution, it is a verification.

<h2 align="center">System Requirements</h1>

```
> min 64 gb ram recommended.
> If you only have access to 32gb ram; start with the SWAP SPACE code. If you are not familiar with swap space, you should roll down and watch the whole video first. If you have more than 32gb ram, skip this step.
```

<h2 align="center">SWAP SPACE</h1>

If you have more than 32gb of ram, skip this step and go directly to the "Circom & SnarkJS Installation" step. Otherwise, follow the steps below. The commands below will add a temporary 32gb ram space by allocating from your server's SSD space. I do not recommend using this on your other nodes etc., as it is not a functional space.

```
sudo swapoff -a
```
```
sudo fallocate -l 32768M /swapfile
```

## Let's set permissions
```
sudo chmod 600 /swapfile
```
## add to system
```
sudo mkswap /swapfile
```
## activate it
```
sudo swapon /swapfile
```

<h2 align="center">Circom & SnarkJS Installation</h1>

```console
# deps
sudo apt-get update
sudo apt-get install -y build-essential git npm

# Rust (to compile circom) > press enter when prompted.
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
. "$HOME/.cargo/env"

# circom (v2.2.x)
git clone https://github.com/iden3/circom.git
cd circom
cargo install --path circom
circom --version
cd ..

# snarkjs
npm install -g snarkjs
snarkjs --version

```

<h2 align="center">Clone Citrea RiscZero-to-BitVM repository</h1>

```console
git clone https://github.com/chainwayxyz/risc0-to-bitvm2
cd risc0-to-bitvm2
```

<h2 align="center">Install Git-lfs and pull large files in the repo</h1>

```console
sudo apt-get install -y git-lfs
git lfs pull
```

<h2 align="center">Generate r1cs files</h1>

```console
# circomlib
git clone https://github.com/iden3/circomlib.git

# Compile -> verify_for_guest.r1cs
sed -i '$d' ./groth16_proof/circuits/stark_verify.circom
circom groth16_proof/circuits/verify_for_guest.circom --r1cs --O2

# (Optional Step) shasum check of the r1cs you generated
sha256sum verify_for_guest.r1cs
# c90be1fb4b83f8f10f4be51e47ebdde2fd97dacd60d658d63d6828f3f642a116
```

<h2 align="center">Setup Powers of Tau and final zkey</h1>

```console
# download full files used for the ceremony (10+ GB)
wget https://pse-trusted-setup-ppot.s3.eu-central-1.amazonaws.com/pot28_0080/ppot_0080_23.ptau
wget https://static.mainnet.citrea.xyz/verify_for_guest_final.zkey

# (Optional Step) check these as well
sha256sum ppot_0080_23.ptau verify_for_guest_final.zkey
# 05085994c8aa34e4925585202e178e09fb8acc04e9565311751e8a04dec81cb1  ppot_0080_23.ptau
# 86fc7478fce8b91b502538cc64e8295a104f68ecf025042a21b10b54d7d504c0  verify_for_guest_final.zkey
```

<h2 align="center">Run verification with SnarkJS</h1>

```console
# increase Node memory for large circuits
export NODE_OPTIONS="--max-old-space-size=32768"

# Verify that zkey transcript matches r1cs and ptau
# Depending on your system, it may take from 30 mins to a few hours
snarkjs zkey verify verify_for_guest.r1cs ppot_0080_23.ptau verify_for_guest_final.zkey
```

> You can [compare](https://gist.github.com/ekrembal/527f44c91736a511f6bfd61e01bc4f22) the result to confirm the accuracy of the Ceremony.


https://github.com/user-attachments/assets/32816585-9cd2-4b89-8905-c5eda2fa4e8a

