# first_cairo_contract
First attempt to deploy a Cairo contract


	1- Setting up a Starknet Account
		a. Setting up the network
			exportSTARKNET_NETWORK=alpha-goerli
			
			From <https://docs.starknet.io/documentation/getting_started/account_setup/> 
			
		b. Choose a wallet provider
			export STARKNET_WALLET=starkware.starknet.wallets.open_zeppelin.OpenZeppelinAccount
			
		c. Create an account
			starknet new_account
			
			
		d. Transfer Goerli Eth to the account
			https://faucet.goerli.starknet.io/
			
		e. Deploy the account
			starknet deploy_account
			
			
			
	2- Writing and deploying the contract
	
		a. Write the contract and name it using a .cairo extension:
	
	
	%lang starknet
	from starkware.cairo.common.cairo_builtins import HashBuiltin
	// Define a storage variable.
	@storage_var
	func balance() -> (res: felt) {
	}
	// Increases the balance by the given amount.
	@external
	func increase_balance{
	    syscall_ptr: felt*,
	    pedersen_ptr: HashBuiltin*,
	    range_check_ptr,
	}(amount: felt) {
	    let (res) = balance.read();
	    balance.write(res + amount);
	    return ();
	}
	// Returns the current balance.
	@view
	func get_balance{
	    syscall_ptr: felt*,
	    pedersen_ptr: HashBuiltin*,
	    range_check_ptr,
	}() -> (res: felt) {
	    let (res) = balance.read();
	    return (res=res);
	}
	
	
		b. Compile the contract:
		starknet-compile contract.cairo \
    --output contract_compiled.json \
    --abi contract_abi.json
	
	From <https://docs.starknet.io/documentation/getting_started/intro/> 
	
		2 files are output: 
			contract_compiled.json
			contract_abi.json
		
		c. Declare the contract on Starknet testnet
		exportSTARKNET_NETWORK=alpha-goerli
		
		starknet declare --contract contract_compiled.json
		
		Get the contract class hash. It will be necessary to deploy the contract.
		
		d. Deploy the contract on Starknet testnet
			starknet deploy --class_hash $CLASS_HASH
			
			The contract will have its address. Set the following env file to interact with the contract:
			
			# The deployment address of the previous contract.
			export CONTRACT_ADDRESS="<address of the previous contract>"
			
		e. Interact with the contract
			Increasing the balance:
			starknet invoke \
		    --address ${CONTRACT_ADDRESS} \
		    --abi contract_abi.json \
		    --function increase_balance \
		    --inputs 1234
		
			Querying the balance:
			starknet call \
    --address ${CONTRACT_ADDRESS}\
    --abi contract_abi.json \
    --function get_balance
