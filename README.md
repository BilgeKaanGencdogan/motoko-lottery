# motoko-lottery
Creating lottery project 

```motoko
import Buffer "mo:base/Buffer";
import Text "mo:base/Text";
import Nat "mo:base/Nat";
import Int "mo:base/Int";
import Time "mo:base/Time";
import Array "mo:base/Array";
import Iter "mo:base/Iter";

actor class Lottery() {
    private stable var wallets : [Text] = [];
    private let chars : [Text] = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "a", "b", "c", "d", "e", "f", "g", "h", "ı", "i", "j", "k", "l", "m", "n", "o", "ö", "p", "r", "s", "ş", "t", "u", "ü", "v", "y", "z"];
    
    // Function to create a random hex string of specified length
    private func createRandomHexString(length: Nat, seed: Int) : Text {
        var result = "";
        for (i in Iter.range(0, length - 1)) {
            let randomIndex = Int.abs(seed + i) % chars.size();
            result := result # chars[randomIndex];
        };
        result
    };

    // Generate specified number of unique wallet addresses
    public func generateWallets(numberOfWallets: Nat) : async Text {
        if (numberOfWallets == 0) {
            return "Please specify a number greater than 0";
        };

        if (numberOfWallets > 100) {
            return "Maximum 100 wallets can be generated at once";
        };

        let walletsBuffer = Buffer.Buffer<Text>(numberOfWallets);

        // Generate unique wallet addresses
        for (i in Iter.range(0, numberOfWallets - 1)) {
            // Use the current time plus the loop index as a seed for uniqueness
            let walletAddress = "0x" # createRandomHexString(40, Int.abs(Time.now()) + i);
            walletsBuffer.add(walletAddress);
        };

        // Update stable storage
        wallets := Buffer.toArray(walletsBuffer);
        
        return "Successfully generated " # Nat.toText(numberOfWallets) # " wallet addresses";
    };

    // Get all generated wallets
    public query func getWallets() : async [Text] {
        wallets
    };

    // Get total number of wallets
    public query func getWalletCount() : async Nat {
        wallets.size()
    };

    // Pick a random winner
    public func pickWinner() : async ?Text {
        if (wallets.size() == 0) {
            return null;
        };
        
        let randomIndex = Int.abs(Time.now()) % wallets.size();
        ?wallets[randomIndex]
    };

    // Clear all wallets
    public func clearWallets() : async Text {
        wallets := [];
        return "All wallets cleared";
    };
};
```
