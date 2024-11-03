# motoko-lottery
Creating lottery project winner picked by modula operation. Random value returns a reminder according to wallet size that is winner's index.

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
`Screen`;

You enter how many wallets you are going to create here, then click 'call' button.
![motoko-1](https://github.com/user-attachments/assets/cec4fa20-f8bd-4e7e-9dd3-d39389f94ebf)

You can get wallets' addresses by pressing calling that function
![motoko-2](https://github.com/user-attachments/assets/6115902a-f27f-480c-8b8f-9faa9176e04f)

And finally, you can pick a winner by calling that function
![motoko-4](https://github.com/user-attachments/assets/24a22f4c-6689-42b1-be2d-568c1554509a)

`Output`;

![motoko-output](https://github.com/user-attachments/assets/4e23b0d9-3c5b-4f7a-8868-d295471d4ff3)




