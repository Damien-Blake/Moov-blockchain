# Moov-blockchain
# Moov-blockchain
Workshop 1

Transcation id for project one


at12qslh9cn2wgp9djl58wunsew3ucwnmcz0y3cpgrz5puu3jc6pyxqpckq35

Code used
// The 'dante_salvador_blake_hello1' program.
program dante_salvador_blake_hello1.aleo {
    transition main(public a: u32, b: u32) -> u32 {
        let c: u32 = a + b; 
        return c;
    }
}

//this allowed the code to 
to deploy a hello world program identifying the data type required for the public and private info//

//using this
leo run main 2u32 3u32 --network testnet

WORKSHOP 2

Transcation id for project two


at13t927g8ur9f9va6unma9v3urd04hfjvfznksp9vr654k7hw04u8stxm2qn

// The 'dante_salvador_token1' program.
program dante_salvador_token1.aleo {
    // The `Token` record datatype.
    record Token {
        // The token owner.
        owner: address,
        // The token amount.
        amount: u64,
    }
    // The `mint` function initializes a new record with the
    // specified number of tokens assigned to the specified receiver.
    transition mint(owner: address, amount: u64) -> Token {
        return Token {
            owner: owner,
            amount: amount,
        };
    }
    // The `transfer` function sends the specified number of tokens
    // to the receiver from the provided token record.
    transition transfer(token: Token, to: address, amount: u64) -> (Token, Token) {

        // Checks the given token record has sufficient balance.
        // This `sub` operation is safe, and the proof will fail
        // if an overflow occurs.
        // `difference` holds the change amount to be returned to sender.
        let difference: u64 = token.amount - amount;

        // Produce a token record with the change amount for the sender.
        let remaining: Token = Token {
            owner: token.owner,
            amount: difference,
        };

        // Produce a token record for the specified receiver.
        let transferred: Token = Token {
            owner: to,
            amount: amount,
        };

        // Output the sender's change record and the receiver's record.
        return (remaining, transferred);
    }
}

// THIS code takes record of transaction carried out then store info about it and calculate what is deducted from the senders account and keeps record of the balance 


Workshop 3

Transcation id for project three


at1jxxl6k2gvxl3z4u3c9czhrhwgh7rzlcvpmc4n9xx5ulkxkch6u8sjntsl2

// The 'dante_projectwork3' program.
// The 'aleo_voice7' program.
program dante_projectwork3.aleo {

    //Record for Voice data
    record Voice {
        owner: address,
        receiver: address,
        msg: u128,
        hash_msg: u128,
    }

    //Record for combining both owner and receiver
    record CoBind {
        hash_owner: field,
        owner: address, 
        receiver: address,
        bind_hash: field,
    }

    // struct data for storing user data
    struct Messaging {
        messenger: field,
        total_messages: u64,
    }

    //Mapping for  user data
    mapping voice_msg: field => Messaging; 

    //send voice transition function with the async await js to update state. has 4 inputs
    async transition send_voice (owner: address, receiver: address, msg: u128,  co_bind: field) -> (Voice, Future){
        //hash a message
        let hash: u128 = BHP256:: hash_to_u128(msg);

        //checks if address calling match owner address
        assert(self.caller == owner);

        //checks address calling is not a receiver
        assert_neq(self.caller, receiver);
        
        //hash the owner
        let hash_owner: field = BHP256:: hash_to_field(owner);

        //hash the receiver
        let hash_receiver: field = BHP256:: hash_to_field(receiver);

        //add both [owner and receiver] hash
        let add_hash : field = hash_owner + hash_receiver ;

        // declaring them into a record -> Voice
        let send_from : Voice = Voice {
            owner: owner,
            receiver: receiver,
            msg: msg,
            hash_msg: hash
        };
        
        //return the record and update the state onchain with the function name - finalize_send_voice
        return (send_from, finalize_send_voice(owner, hash));

    }
    //state being updated
    async function finalize_send_voice (owner: address, hash: u128){
        //hash the owner
        let hash_owner: field = BHP256:: hash_to_field(owner);
        // getting the mapping detail, by default mapping is to -> hash_owner  and 0 total messages
        let total: Messaging = Mapping::get_or_use(voice_msg, hash_owner, Messaging{
            messenger: hash_owner,
            total_messages: 0u64,
        });
        
        //storing/setting the data into the mapping, now the data component will be uopdate to the hash_owner and n total messages
        voice_msg.set(hash_owner, Messaging {
            messenger: hash_owner,
            total_messages: total.total_messages + 1u64,
        });
    }

    transition combine_hash_owner_receiver(owner: address, receiver: address) -> (CoBind){
        assert_eq(owner, self.caller);
        let hash_owner: field = BHP256::hash_to_field(owner);
        let hash_receiver: field = BHP256::hash_to_field(receiver);
        let add_hash: field = hash_owner + hash_receiver;

        let update_hash: CoBind = CoBind {
            hash_owner: hash_owner,
            receiver: receiver,
            owner: owner,
            bind_hash: add_hash,
        };
        return (update_hash);
    }
}

this allowed the info gotten from the sender and reciever to be stored and using updates every detail after transaction

