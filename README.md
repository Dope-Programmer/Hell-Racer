# Hell-Racer

 In an 8-week group project using Unreal Engine and C++, I worked with a team of talented graphic designers and programmers. My main role was to build the entire power-up system from scratch, using Enumerators to manage different types of power-ups. Due to time constraints, the game included only two power-ups, but I designed the system to make it easy to add more in the future. I also developed the user interface (UI) for the power-up system to create a smooth and intuitive experience for players.

## Track overview
![track overview 1](https://github.com/user-attachments/assets/1693034f-9439-4b52-8ca0-78bac794050d)
![track overview 2](https://github.com/user-attachments/assets/282c0365-1d73-41e5-b4f3-c39252a6035d)

## PowerManager
A script I would like to show is my PowerUpManager script. The script in all is around 300 rows, so I have chosen some highlights.
Purpose: It manages the relationship between the player, the power-ups, and the UI. This includes selecting, applying, and handling power-ups during gameplay.
eller: The PowerUpManager class is the core component responsible for managing power-ups in the game. It handles the selection, storage, and activation of power-ups for the player while integrating seamlessly with the game's user interface (UI). The manager uses enumerators to differentiate between types of power-ups and applies weighted randomness to ensure a balanced distribution. 

### Key Methods: 

#### SwitchPU Method: 

Manages the selection and application of a power-up to the player. This method checks the current state of power-up storage, selects a new power-up based on weighted randomness, and updates the UI accordingly.

<details>
<summary>Click to expand</summary>
  
    void UPowerUpManager::SwitchPU(EPowerUpTypes types)
    {
    TheCharacter = Cast<ACharacterInput>(GetOwner()); 
    PUWidget = TheCharacter->PowerUpUI; 

    if (IsListFull) 
    {
        return; 
    }

    types = RandomPowerUp(); 
    AddPowerUp(types); 

    if (TheCharacter && PUWidget)
    {
        PUWidget->ColorSymbols(types); 
    }
    }

</details>

#### RandomPowerUp Method: 

Since our game was mostly inspired my mario kart, we wanted a similar system with power-up:s with a random selection. In this method, it selects a power-up randomly based on weighted probabilities. This ensures that more common power-ups are selected more frequently, adding a dynamic and unpredictable element to gameplay.

<details>
<summary>Click to expand</summary>

    EPowerUpTypes UPowerUpManager::RandomPowerUp()
    {
    TotalWeight = 0; 

    for (int32 Weight : PowerUpWeights)
    {
        TotalWeight += Weight;
    }

    RandomWeight = FMath::RandRange(1, TotalWeight); 
    CurrentWeight = 0;

    for (int32 i = 0; i < PowerUpWeights.Num(); ++i)
    {
        CurrentWeight += PowerUpWeights[i];
        if (RandomWeight <= CurrentWeight)
        {
            return static_cast<EPowerUpTypes>(i);
        }
    }

    return EPowerUpTypes::None; 
    }

</details>

#### AddPowerUp Method:
This method adds a newly selected power-up to the player’s list if there is space available. It also checks for any errors, such as invalid power-up types, and provides feedback via the Unreal Engine logging system.

<details>
<summary>Click to expand</summary>

    void UPowerUpManager::AddPowerUp(EPowerUpTypes types)
    {
    FString PowerUpName = PowerUpTypeToString(types);

    if (IsListFull)
    {
        return; 
    }

    if (types == EPowerUpTypes::None || types >= EPowerUpTypes::Max_PU)
    {
        UE_LOG(LogTemp, Error, TEXT("Invalid power-up type: %d"), static_cast<int32>(types));
        return;
    }

    for (int32 i = 0; i < PowerUpArray.Num(); i++)
    {
        if (PowerUpArray[i] == EPowerUpTypes::None) 
        {
            PowerUpArray[i] = types; 
            IsListFull = true;
            CheckPUList(); 
            break;
        }
    }
    }

</details>

#### StartPU Method:

It activates the power-up that is currently selected in the player's list. Depending on the type of power-up, it finds the appropriate component on the player character and applies the desired effect, such as speed boost or size reduction.

<details>
<summary>Click to expand</summary>

    void UPowerUpManager::StartPU()
    {
    ACharacter* OwnerCH = Cast<ACharacter>(GetOwner()); 

    if (!OwnerCH)
    {
        UE_LOG(LogTemp, Error, TEXT("Owner character not found"));
        return;
    }

    SpeedComponent = OwnerCH->FindComponentByClass<UMySpeedBoostComponent>(); 
    UMySmallSizeBoost* SizeComponent = OwnerCH->FindComponentByClass<UMySmallSizeBoost>();

    for (int32 i = 0; i < PowerUpArray.Num(); i++)
    {
        EPowerUpTypes PUTypes = PowerUpArray[i];

        if (PUTypes == EPowerUpTypes::Small_Size && SizeComponent)
        {
            SizeComponent->SmallSizePicked = true;
            SizeComponent->ImplementSizeChange();
            PowerUpUI->ColorSymbols(PUTypes);
            PlaySizeSound(1);
        }
        else if (PUTypes == EPowerUpTypes::Speed_Boost && SpeedComponent)
        {
            SpeedComponent->SpeedBoostPickedUp = true;
            SpeedComponent->AddBoost();
            PowerUpUI->ColorSymbols(PUTypes);
        }

        PowerUpArray[i] = EPowerUpTypes::None; 
        CheckPUList(); 
        break; 
    }
    }

</details>

## Result: 

With some of the key methods explained, this was the result: 

#### Speed boost: 

![SpeedBoostPowerUp-gif](https://github.com/user-attachments/assets/35a4a46d-6ca1-433f-8cf7-2d0ed4a25287)

#### Small size boost: 

![SmallSizePowerUp-gif](https://github.com/user-attachments/assets/1dba43e7-ed10-4d6a-b12c-4c66f1ceb3cd)

