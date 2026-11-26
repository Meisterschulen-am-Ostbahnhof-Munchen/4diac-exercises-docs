# Uebung_137_AX: Übung zu STRING über CAN empfangen (bis 8 Zeichen), Anzeige im Watch

This article describes the 4diac IDE sub-application Uebung_137_AX (Übung zu STRING über CAN empfangen (bis 8 Zeichen), Anzeige im Watch).

----

![Uebung_137_AX_network](./Uebung_137_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Übung zu STRING über CAN empfangen (bis 8 Zeichen), Anzeige im Watch**

-----

## Description and Components

The exercise consists of the sub-application Uebung_137_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **STRUCT_DEMUX_3**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **NmGetCfInfo_1**: Instance of type isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = thisMember
  - Parameter address = ADD_ALL_PASS
  - Parameter mask = FLT_ALL_PASS
- **STRUCT_DEMUX_4**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_DEMUX_5**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **NetEv2NetEv**: Instance of type isobus::pgn::NetEv2NetEv.
  - Parameter s16Handle = GLOBAL_A
- **AlPgnRxNew8B**: Instance of type isobus::pgn::rx::AlPgnRxNew8B.
  - Parameter u32Pgn = PGN_PDU1_PropA
  - Parameter u16DaSize = 8
  - Parameter u8Priority = 3
- **CAN_MSG_TO_STRING**: Instance of type isobus::pgn::CAN_MSG_TO_STRING.
- **NetEv_Install**: Nested sub-application (SubApp) MyLib::sys::NetEv_Install_Filter.

The exercise uses the common pattern with `NmGetCfInfo` (IND/RSP): `IND` triggers `STRUCT_DEMUX`, `NetEv_Install` handles the event and acknowledges with `RSP`.

-----

## Summary

Exercise Uebung_137_AX demonstrates the modular IEC 61499 architecture in 4diac IDE.
