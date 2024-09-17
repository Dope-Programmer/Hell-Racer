# Hell-Racer

 In an 8-week group project using Unreal Engine and C++, I worked with a team of talented graphic designers and programmers. My main role was to build the entire power-up system from scratch, using Enumerators to manage different types of power-ups. Due to time constraints, the game included only two power-ups, but I designed the system to make it easy to add more in the future. I also developed the user interface (UI) for the power-up system to create a smooth and intuitive experience for players.

## Track overview
![track overview 1](https://github.com/user-attachments/assets/1693034f-9439-4b52-8ca0-78bac794050d)
![track overview 2](https://github.com/user-attachments/assets/282c0365-1d73-41e5-b4f3-c39252a6035d)

## PowerManager
A script I would like to show is my PowerUpManager script. 
Purpose: It manages the relationship between the player, the power-ups, and the UI. This includes selecting, applying, and handling power-ups during gameplay.
eller: The PowerUpManager class is the core component responsible for managing power-ups in the game. It handles the selection, storage, and activation of power-ups for the player while integrating seamlessly with the game's user interface (UI). The manager uses enumerators to differentiate between types of power-ups and applies weighted randomness to ensure a balanced distribution. Since our game was mostly inspired my mario kart, we wanted a similar system with power-up:s.

### Key features/functionality

#### SwitchPU Method: 

Manages the selection and application of a power-up to the player. This method checks the current state of power-up storage, selects a new power-up based on weighted randomness, and updates the UI accordingly.

<details>
<summary>Click to expand</summary>
  
    void UPowerUpManager::SwitchPU(EPowerUpTypes types)
    {
    TheCharacter = Cast<ACharacterInput>(GetOwner()); // Get the player
    PUWidget = TheCharacter->PowerUpUI; // Reference to the UI widget

    if (IsListFull) // Check if the power-up list is full
    {
        return; // Exit if the list is full
    }

    types = RandomPowerUp(); // Select a random power-up
    AddPowerUp(types); // Add the selected power-up

    if (TheCharacter && PUWidget)
    {
        PUWidget->ColorSymbols(types); // Update the UI with the selected power-up
    }
    }

</details>




