# 2026-06-22 SGP40 Raw Read and CRC

GY-SGP is on the same I2C bus as AHT20:

```text
AHT20  -> 0x38
SGP40  -> 0x59
```

The first SGP40 read returned a very high value:

```text
sgp=ok,sgp_raw=65529
```

This was recorded as a startup value, not as an air-quality result.

After a few more reads, the current desk environment stayed around:

```text
sgp_raw=29103
sgp_raw=29655
sgp_raw=29692
sgp_raw=29746
sgp_raw=29817
```

So the current local baseline is roughly:

```text
sgp_raw ~= 29100-29800
temp    ~= 30.0-30.2 C
hum     ~= 57.8-60.2 %
```

CRC check was then added for the two raw bytes returned by SGP40. After that,
the status was still valid:

```text
id=L4-001,bat=100,state=normal,aht=ok,temp=30.0,hum=62.0,env=normal,sgp=ok,sgp_raw=28928
```

This confirms the SGP40 read is not just an address scan result. The sensor
returns data and the returned CRC matches.

Raw values are only the sensor signal. They are not a VOC score. The next step
is to use Sensirion's VOC Index algorithm and report a `voc` value instead of
making decisions directly from `sgp_raw`.
