# ios uuid simulator booted check
>  xcrun simctl list 'devices' 'booted'

## How check idb tracking divice error
> 1. xcrun simctl list 'devices' 'booted' select the tartget
> 2. idb connect TARGET_UUID
> 3. idb log
