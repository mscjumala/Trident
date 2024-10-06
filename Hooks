-- just some hooking lib for ez unhooking, minhook but for roblox lmao

local MinHook = {Hooks = {}} do
    function MinHook:Add(Data, Type, ...)
        local Args = {...} -- if u see this is is just a ghetto fix for hooking functions without upvalues because im lazy
        local Hook = {Data = Data, Type = Type}

        table.insert(self.Hooks, Hook)

        if Type == "Index" then
            Hook.Original = rawget(Data, Args[1])
            Hook.Index = Args[1]
    
            rawset(Data, Args[1], Args[2])
        elseif Type == "Upvalue" then
            Hook.Original = getupvalue(Data, Args[1])
            Hook.Index = Args[1]

            setupvalue(Data, Args[1], Args[2])
        elseif string.sub(Type, 1, 2) == "__" then
            Hook.Original = type(Data) == "table" and getmetatable(Data)[Type] or hookmetamethod(Data, Type, Args[1])

            if type(Data) == "table" then setmetatable(Data, {[Type] = Args[1]}) end
        elseif Type == "Closure" then
            local Func = Args[1]
            Hook.Original = replaceclosure(Data, function(...)
		        return Func(...) -- dirty fix for "too many upvalues" error
	        end)
        end

        return Hook.Original
    end

    function MinHook:Reset()
        for _, Hook in next, self.Hooks do
            local Type = Hook.Type

            if Type == "Index" then
                rawset(Hook.Data, Hook.Index, Hook.Original)
            elseif Type == "Upvalue" then
                setupvalue(Hook.Data, Hook.Index, Hook.Original)
            elseif Type == "Closure" then
                replaceclosure(Hook.Data, Hook.Original)
            elseif string.sub(Type, 1, 2) == "__" then
                if type(Hook.Data) == "table" then 
                    setmetatable(Hook.Data, {[Type] = Hook.Original}) 
                else 
                    hookmetamethod(Hook.Data, Type, newcclosure(Hook.Original))
                end
            end
        end
    end
end

return MinHook
