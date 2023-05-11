#include <iostream>
#include <vector>
#include <algorithm>

class Asset {
public:
    std::string symbol;
    std::string name;
    double price;
    int quantity;

    Asset(std::string sym, std::string n, double p, int q)
        : symbol(sym), name(n), price(p), quantity(q) {}
};

class User {
public:
    std::string username;
    double accountBalance;
    std::vector<Asset> portfolio;

    User(std::string name, double balance)
        : username(name), accountBalance(balance) {}

    void BuyAsset(Asset asset, int quantity) {
        double totalPrice = asset.price * quantity;
        if (totalPrice > accountBalance) {
            std::cout << "Insufficient funds to buy " << quantity << " shares of " << asset.symbol << std::endl;
            return;
        }
        if (quantity > asset.quantity) {
            std::cout << "Not enough shares available for " << asset.symbol << std::endl;
            return;
        }

        // Deduct the purchase amount from the account balance
        accountBalance -= totalPrice;

        // Check if the asset is already in the portfolio
        auto it = std::find_if(portfolio.begin(), portfolio.end(),
                               [asset](const Asset& a) { return a.symbol == asset.symbol; });

        if (it != portfolio.end()) {
            // If the asset exists in the portfolio, update the quantity
            it->quantity += quantity;
        } else {
            // If the asset is not in the portfolio, add it
            portfolio.push_back(Asset(asset.symbol, asset.name, asset.price, quantity));
        }

        // Update the asset quantity
        asset.quantity -= quantity;

        std::cout << "Successfully bought " << quantity << " shares of " << asset.symbol << std::endl;
    }

    void SellAsset(Asset asset, int quantity) {
        // Check if the asset is in the portfolio
        auto it = std::find_if(portfolio.begin(), portfolio.end(),
                               [asset](const Asset& a) { return a.symbol == asset.symbol; });

        if (it == portfolio.end() || it->quantity < quantity) {
            std::cout << "You don't have enough shares of " << asset.symbol << " to sell" << std::endl;
            return;
        }

        // Calculate the sale amount
        double salePrice = asset.price * quantity;

        // Update the account balance
        accountBalance += salePrice;

        // Update the asset quantity
        it->quantity -= quantity;

        std::cout << "Successfully sold " << quantity << " shares of " << asset.symbol << std::endl;
    }

    void PrintPortfolio() {
        std::cout << "Portfolio for user: " << username << std::endl;
        std::cout << "-------------------------" << std::endl;

        for (const Asset& asset : portfolio) {
            std::cout << "Symbol: " << asset.symbol << std::endl;
            std::cout << "Name: " << asset.name << std::endl;
            std::cout << "Price: " << asset.price << std::endl;
            std::cout << "Quantity: " << asset.quantity << std::endl;
            std::cout << "-------------------------" << std::endl;
        }
    }
};

int main() {
    // Create a user and initialize the account balance
    User user("John", 10000.0);

    // Create some sample assets
    Asset asset1("AAPL", "Apple Inc.", 150
