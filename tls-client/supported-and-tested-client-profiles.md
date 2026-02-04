# Supported and tested Client Profiles

### Internal Client Profiles

The internal client profiles were created and tested by myself. Of course that does not mean they are 100% correct. But i tested them more precise then the contributed profiles.&#x20;

1. Chrome
   * 103 (chrome\_103)
   * 104 (chrome\_104)
   * 105 (chrome\_105)
   * 106 (chrome\_106)
   * 107 (chrome\_107)
   * 108 (chrome\_108)
   * 109 (chrome\_109)
   * 110 (chrome\_110)
   * 111 (chrome\_111)
   * 112 (chrome\_112)
   * 116 with PSK (chrome\_116\_PSK)
   * 116 with PSK and PQ (chrome\_116\_PSK\_PQ)
   * 117 (chrome\_117)
   * 120 (chrome\_120)
   * 124 (chrome\_124)
   * 130 with PSK (chrome\_130\_PSK)
   * 133 (chrome\_133)
   * 133 with PSK (chrome\_133\_PSK)
2. Safari
   * 15.6.1 (safari\_15\_6\_1)
   * 16.0 (safari\_16\_0)
3. iOS (Safari)
   * 15.5 (safari\_ios\_15\_5)
   * 15.6 (safari\_ios\_15\_6)
   * 16.0 (safari\_ios\_16\_0)
   * 17.0 (safari\_ios\_17\_0)
   * 18.0 (safari\_ios\_18\_0)
   * 18.5 (safari\_ios\_18\_5)
4. iPadOS (Safari)
   * 15.6 (safari\_ipad\_15\_6)
5. Firefox
   * 102 (firefox\_102)
   * 104 (firefox\_104)
   * 105 (firefox\_105)
   * 106 (firefox\_106)
   * 108 (firefox\_108)
   * 110 (firefox\_110)
   * 117 (firefox\_117)
   * 132 (firefox\_132)
6. Opera
   * 89 (opera\_89)
   * 90 (opera\_90)
   * 91 (opera\_91)
7. Custom Clients
   * Zalando iOS Mobile (zalando\_ios\_mobile)
   * Nike IOS Mobile (nike\_ios\_mobile)
   * Cloudscraper
   * MMS IOS (mms\_ios or mms\_ios\_1)
   * MMS IOS 2 (mms\_ios\_2)
   * MMS IOS 3 (mms\_ios\_3)
   * Mesh IOS (mesh\_ios or mesh\_ios\_1)
   * Confirmed IOS (confirmed\_ios)

### Contributed Client Profiles

The contributed client profiles are contributed by people from the community. They are not precisely tested like the internal ones. Please be aware that they might have issues.

1. Chrome
   * 131 (chrome\_131)
   * 131 with PSK (chrome\_131\_PSK)
2. OkHttp4
   * Android 7 (okhttp4\_android\_7)
   * Android 8 (okhttp4\_android\_8)
   * Android 9 (okhttp4\_android\_9)
   * Android 10 (okhttp4\_android\_10)
   * Android 11 (okhttp4\_android\_11)
   * Android 12 (okhttp4\_android\_12)
   * Android 13 (okhttp4\_android\_13)
3. Custom Clients
   * Zalando Android Mobile (zalando\_android\_mobile)
   * Nike Android Mobile (nike\_android\_mobile)
   * Mesh IOS 2 (mesh\_ios\_2)
   * Mesh Android (mesh\_android or mesh\_android\_1)
   * Mesh Android 2 (mesh\_android\_2)
   * Confirmed Android (confirmed\_android)
   * Confirmed Android 2 (confirmed\_android\_2)
4. Firefox
   * 120 (firefox\_120)
   * 123 (firefox\_123)
   * 132 (firefox\_132)
   * 133 (firefox\_133)

You can also provide your own client. See the examples how to do that.

All Clients support Random TLS Extension Order by setting the option on the HTTP client itself `WithRandomTLSExtensionOrder()`. \
This is needed for Chrome 107+.&#x20;

#### Shared Library & Standalone Api

When working with the Shared Library or Standalone API you need to set `"withRandomExtensionOrder":true`

