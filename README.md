A 6x6 encoder macropad using 21 pins.

# qmk source tree

https://github.com/xyzz/vial-qmk/tree/knobpad

# chibios patch for pin A9 on stm32f4xxx

```diff
diff --git a/os/hal/ports/STM32/LLD/OTGv1/hal_usb_lld.c b/os/hal/ports/STM32/LLD/OTGv1/hal_usb_lld.c
index 0f48d1fd0..38aff8554 100644
--- a/os/hal/ports/STM32/LLD/OTGv1/hal_usb_lld.c
+++ b/os/hal/ports/STM32/LLD/OTGv1/hal_usb_lld.c
@@ -40,18 +40,18 @@
 
 #if STM32_OTG_STEPPING == 1
 #if defined(BOARD_OTG_NOVBUSSENS)
-#define GCCFG_INIT_VALUE        (GCCFG_NOVBUSSENS | GCCFG_VBUSASEN |        \
-                                 GCCFG_VBUSBSEN | GCCFG_PWRDWN)
+#define GCCFG_INIT_VALUE        (GCCFG_NOVBUSSENS |        \
+                                 GCCFG_PWRDWN)
 #else
-#define GCCFG_INIT_VALUE        (GCCFG_VBUSASEN | GCCFG_VBUSBSEN |          \
+#define GCCFG_INIT_VALUE        (GCCFG_NOVBUSSENS |          \
                                  GCCFG_PWRDWN)
 #endif
 
 #elif STM32_OTG_STEPPING == 2
 #if defined(BOARD_OTG_NOVBUSSENS)
-#define GCCFG_INIT_VALUE        GCCFG_PWRDWN
+#define GCCFG_INIT_VALUE        (GCCFG_NOVBUSSENS | GCCFG_PWRDWN)
 #else
-#define GCCFG_INIT_VALUE        (GCCFG_VBDEN | GCCFG_PWRDWN)
+#define GCCFG_INIT_VALUE        (GCCFG_NOVBUSSENS | GCCFG_VBDEN | GCCFG_PWRDWN)
 #endif
 
 #endif
@@ -815,7 +815,7 @@ void usb_lld_start(USBDriver *usbp) {
     otgp->PCGCCTL = 0;
 
     /* VBUS sensing and transceiver enabled.*/
-    otgp->GOTGCTL = GOTGCTL_BVALOEN | GOTGCTL_BVALOVAL;
+    // otgp->GOTGCTL = GOTGCTL_BVALOEN | GOTGCTL_BVALOVAL;
 
 #if defined(BOARD_OTG2_USES_ULPI)
 #if STM32_USB_USE_OTG1
diff --git a/os/hal/ports/STM32/LLD/OTGv1/hal_usb_lld.h b/os/hal/ports/STM32/LLD/OTGv1/hal_usb_lld.h
index 69a5ab603..200b4b91a 100644
--- a/os/hal/ports/STM32/LLD/OTGv1/hal_usb_lld.h
+++ b/os/hal/ports/STM32/LLD/OTGv1/hal_usb_lld.h
@@ -534,7 +534,7 @@ struct USBDriver {
  * @notapi
  */
 #if (STM32_OTG_STEPPING == 1) || defined(__DOXYGEN__)
-#define usb_lld_connect_bus(usbp) ((usbp)->otg->GCCFG |= GCCFG_VBUSBSEN)
+#define usb_lld_connect_bus(usbp)
 #else
 #define usb_lld_connect_bus(usbp) ((usbp)->otg->DCTL &= ~DCTL_SDIS)
 #endif
@@ -545,7 +545,7 @@ struct USBDriver {
  * @notapi
  */
 #if (STM32_OTG_STEPPING == 1) || defined(__DOXYGEN__)
-#define usb_lld_disconnect_bus(usbp) ((usbp)->otg->GCCFG &= ~GCCFG_VBUSBSEN)
+#define usb_lld_disconnect_bus(usbp)
 #else
 #define usb_lld_disconnect_bus(usbp) ((usbp)->otg->DCTL |= DCTL_SDIS)
 #endif
```