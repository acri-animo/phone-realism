# FiveM Phone Realism (Sandbox/Mythic framework)

Replacing the anim.lua in sandbox-phone > client with this one allows you to link colored phone props to the color of phone case in the UI so they match when a player opens their phone.

I am not providing phone props but recommend DDesigns. You will need to replace the prop names to match the ones that you add.

Ultimately, the phone case items in sandbox-inventory need to have the correct color set for the phone case arg and then those colors need to be utilized in the getPhoneModelFromColor function in this anim.lua file to return the respective colored prop.

```lua
{
		name = "case_blue",
		label = "Blue Phone Case",
		price = 1000,
		isUsable = true,
		isRemoved = false,
		isStackable = false,
		type = 1,
		rarity = 1,
		closeUi = true,
		weight = 0,
		isDestroyed = false,
		durability = (60 * 60 * 24 * 5),
		phoneCase = "blue", -- This sets the color of the phone case in Mongo
		animConfig = {
			time = 3000,
			pbConfig = {
				label = "Applying Case",
				useWhileDead = false,
				canCancel = true,
				vehicle = false,
				disarm = true,
				ignoreModifier = true,
				disableMovement = false,
				disableCarMovement = false,
				disableMouse = false,
				disableCombat = true,
			},
		},
		metalic = false,
	},
```
```lua
function getPhoneModelFromColor(color)
    if color == "green" then
        return `dd_phone_green`
    elseif color == "grey" then
        return `dd_phone_grey`
	elseif color == "blue" then
		return `dd_phone_blue`
	elseif color == "pink" then
		return `dd_phone_pink`
	elseif color == "purple" then
		return `dd_phone_purple`
	elseif color == "red" then
		return `dd_phone_red`
	elseif color == "white" then
		return `dd_phone_silver`
	elseif color == "gold" then
		return `dd_phone_yellow`
    else
        return `dd_phone_grey`
    end
end

function newPhoneProp()
    Callbacks:ServerCallback("Phone:GetPhoneCase", {}, function(phonecase)
		local phoneColor = phonecase or "default"
		local phoneModel = getPhoneModelFromColor(phoneColor)

        deletePhone()

        RequestModel(phoneModel)
        while not HasModelLoaded(phoneModel) do
            Wait(1)
        end

        phoneProp = CreateObject(phoneModel, 1.0, 1.0, 1.0, true, true, false)
        SetEntityCollision(phoneProp, false, false)

        local bone = GetPedBoneIndex(LocalPlayer.state.ped, 28422)
        AttachEntityToEntity(phoneProp, LocalPlayer.state.ped, bone, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, true, true, false, false, 2, true)

        CreateThread(function()
            while LocalPlayer.state.phoneOpen or _call ~= nil do
                Wait(3)
            end
            deletePhone()
        end)
    end)
end