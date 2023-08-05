```Python

#!/usr/bin/env python3

import pyotp

totp = pyotp.TOTP("abc123")

print("Current OTP:", totp.now())
```
