# Competitor Pricing Model A Minimal Working Example

In game theory, understanding competitor behavior is crucial for strategic decision-making. This article demonstrates a minimal working example of modeling competitor pricing using a simple duopoly game-theoretic approach.

## The Core Concept

We model two competing agents (A and B) setting prices for identical products. Each agent's profit depends on both their own price and competitor's price, following a basic demand function with strategic interaction.

```python
import numpy as np
from scipy.optimize import minimize_scalar

class CompetitorPricingModel:
    def __init__(self, market_size=1000, cost_per_unit=20):
        self.market_size = market_size
        self.cost_per_unit = cost_per_unit
    
    def demand_function(self, price_a, price_b):
        """Linear demand function with competitor effect"""
        # Price sensitivity and competition factor
        alpha = 500
        beta = 0.3
        gamma = 0.1
        
        demand_a = alpha - beta * price_a + gamma * price_b
        return max(0, demand_a)
    
    def profit_function(self, price_a, price_b):
        """Profit calculation for agent A"""
        demand_a = self.demand_function(price_a, price_b)
        revenue_a = price_a * demand_a
        cost_a = self.cost_per_unit * demand_a
        return revenue_a - cost_a
    
    def best_response(self, competitor_price):
        """Find optimal price given competitor's price"""
        def negative_profit(price):
            return -self.profit_function(price, competitor_price)
        
        result = minimize_scalar(negative_profit, bounds=(0, 100), method='bounded')
        return result.x

# Example usage
model = CompetitorPricingModel(market_size=1000, cost_per_unit=20)

# Initial competitor price
competitor_price = 50.0

# Find optimal response
optimal_price = model.best_response(competitor_price)
print(f"Competitor price: ${competitor_price}")
print(f"Optimal response: ${optimal_price:.2f}")

# Simulate multiple iterations
print("\nIterative pricing simulation:")
current_price = 40.0
for i in range(5):
    optimal_response = model.best_response(current_price)
    print(f"Iteration {i+1}: Price = ${current_price:.2f} → Response = ${optimal_response:.2f}")
    current_price = optimal_response
```

## Understanding the Model

The model uses a linear demand function where:
- `alpha` represents base market demand (500 units)
- `beta` captures own-price sensitivity (0.3 units per dollar decrease)
- `gamma` measures competitor effect (0.1 units increase when competitor raises price)

Each agent maximizes profit considering their demand response to both prices.

## Key Parameters

| Parameter | Value | Description |
|----------|-------|-------------|
| Market Size | 1000 | Total potential customers |
| Cost per Unit | $20 | Fixed production cost |
| Price Sensitivity (β) | 0.3 | How much demand drops per dollar increase |
| Competition Effect (γ) | 0.1 | How competitor's price affects demand |

## Advanced Integration

```python
def simulate_competitive_equilibrium(model, initial_price_a=40, initial_price_b=45, iterations=10):
    """Simulate convergence to Nash equilibrium"""
    prices_a = [initial_price_a]
    prices_b = [initial_price_b]
    
    for i in range(iterations):
        # Agent A responds to B's price
        new_price_a = model.best_response(prices_b[-1])
        
        # Agent B responds to A's price  
        new_price_b = model.best_response(prices_a[-1])
        
        prices_a.append(new_price_a)
        prices_b.append(new_price_b)
    
    return prices_a, prices_b

# Run simulation
prices_a, prices_b = simulate_competitive_equilibrium(model)

print("Equilibrium Prices:")
for i, (a, b) in enumerate(zip(prices_a, prices_b)):
    print(f"Step {i}: A=${a:.2f}, B=${b:.2f}")
```

## FAQ

**Q: How does this model handle market saturation?**
A: The demand function includes a maximum capacity constraint through the linear relationship. When prices exceed market willingness to pay, demand drops to zero, preventing unrealistic overconsumption scenarios.

**Q: Can I customize the competition parameters?**
A: Yes, adjust `beta` for own-price sensitivity and `gamma` for competitor impact. Higher gamma means stronger price competition effects, while higher beta makes demand more sensitive to own pricing.

**Q: What's the computational complexity?**
A: The model runs in O(1) time per calculation using analytical optimization. For full simulations with multiple iterations, complexity scales linearly with iteration count.

## Get it

This model provides a foundation for strategic pricing decisions in competitive environments. It enables rapid prototyping of multi-agent interactions without extensive simulation overhead.

[Get the Game-Theoretic Multi-Agent Strategy Pack](https://ptrk-en.gumroad.com/l/math-game-theory-multiagent?offer_code=Launch40)

The pack includes this competitor model plus additional tools for multi-agent coordination, strategic interaction analysis, and competitive market simulation.