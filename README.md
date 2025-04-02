# PokemonDeltaScripts
-- Roblox Pokemon Delta Script
local DataStoreService = game:GetService("DataStoreService")
local PokemonStore = DataStoreService:GetDataStore("PokemonStorage")

-- Pokemon Storage
local PokemonStorage = {}

-- Function to create a new Pokemon
function CreatePokemon(name, level, maxHealth, attack, defense, catchRate)
    return {
        Name = name,
        Level = level,
        MaxHealth = maxHealth,
        CurrentHealth = maxHealth,
        Attack = attack,
        Defense = defense,
        CatchRate = catchRate
    }
end

-- Function to catch a Pokemon
function CatchPokemon(player, pokemon)
    local chance = math.random(1, 100)
    if chance <= pokemon.CatchRate then  -- Catch based on rate
        table.insert(PokemonStorage, pokemon)
        print(player.Name .. " caught a " .. pokemon.Name .. "!")
        PokemonStore:SetAsync(player.UserId, PokemonStorage)
    else
        print("The " .. pokemon.Name .. " escaped!")
    end
end

-- Wild Pokemon Encounter
function WildEncounter()
    local wildPokemons = {
        CreatePokemon("Pidgey", 3, 20, 5, 3, 70),
        CreatePokemon("Rattata", 4, 22, 6, 4, 60)
    }
    return wildPokemons[math.random(1, #wildPokemons)]
end

-- Battle Function
function Battle(playerPokemon, wildPokemon)
    print(playerPokemon.Name .. " vs " .. wildPokemon.Name)
    while playerPokemon.CurrentHealth > 0 and wildPokemon.CurrentHealth > 0 do
        local damageToWild = math.max(1, playerPokemon.Attack - wildPokemon.Defense)
        wildPokemon.CurrentHealth = wildPokemon.CurrentHealth - damageToWild
        print(playerPokemon.Name .. " deals " .. damageToWild .. " damage to " .. wildPokemon.Name)
        
        if wildPokemon.CurrentHealth <= 0 then
            print(wildPokemon.Name .. " fainted!")
            break
        end
        
        local damageToPlayer = math.max(1, wildPokemon.Attack - playerPokemon.Defense)
        playerPokemon.CurrentHealth = playerPokemon.CurrentHealth - damageToPlayer
        print(wildPokemon.Name .. " deals " .. damageToPlayer .. " damage to " .. playerPokemon.Name)
        
        if playerPokemon.CurrentHealth <= 0 then
            print(playerPokemon.Name .. " fainted!")
        end
    end
end

-- Example Usage
local player = {Name = "Ash", UserId = 12345}
local pikachu = CreatePokemon("Pikachu", 5, 35, 10, 5, 50)
local wildPokemon = WildEncounter()

print("A wild " .. wildPokemon.Name .. " appeared!")
Battle(pikachu, wildPokemon)
CatchPokemon(player, wildPokemon)
