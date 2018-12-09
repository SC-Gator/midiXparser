# midiXparser
A small footprint midi parser class


The midiXparser class allows to create rapidily "low level" midi parsers with a small footprint in your applications.
It is platform agnostic so can be used with the Arduino platform or with the AVR c++ compiler.

midiXParser class has a full midi specs coverage, including running status and sysex, and do not rely on serial ports. 
You can pass a byte coming from an array (as done in demo sketch) or even a file.

The demo sketch contains many tests to validate midiXparser features you can use as examples for your own code.

### Examples

#### Declare a parser with a filter on Note off/ Note on


    midiXparser midiParser;
    midiParser.setChannelVoiceMsgFilter(midiXparser::noteOffMsk | midiXparser::noteOnMsk );  
    loop() 
    {

        if ( Serial.available() && ( midiParser.parse( Serial.read() ) {
        // Do something for notes on/off

        } 
        (...)  
    }

#### Capture sysex messages

    midiXparser midiParser;
    midiParser.setSysExFilter(true,64);
    loop() 
    {
        if ( Serial.available() && ( midiParser.parse( Serial.read() ) {
        // Do something for sysex
          if ( midiParser->getMidiMsgType() == midiXparser::sysExMsgType ) {
            for (int j = 0 ; j < midiParser.getSysExMsgLen() ; j++) { 
                Serial.print(midiParser->getSysExMsg()[j],HEX);
                Serial.print(" - ");            
                (...)  
            } 
           }
           (...)  
        }
    
#### Remove Active sense, clock and SongPointer from the MIDI flow

    midiXparser midiParser;
    midiParser.setRealTimeMsgFilter(midiXparser::activeSensingMsk | midiXparser::timingClockMsk );
    midiParser.setSystemCommonMsgFilter(midiXparser::songPosPointerMsk);
    loop() 
    {    
        if ( Serial.available() ) {
        
           if ( midiParser.parse( Serial.read() ) {
              // Do something for active sense, clock and song pos

           } else if (!midiParser.isByteCaptured()) Serial.write(midiParser.getByte() ) ;
           (...)
        }
    }


### Methods

#### bool parse(byte readByte)

    Parse a byte and return true if a midi message was detected, according to the filters set.

#### uint8_t getMidiMsgLen()

    Return the length of the last parsed message (value varies from 1 to 255).
    Return 0 if the parse method does not return true at the last call.    
    
#### bool isSysExMode()

    Return true if the last call to the parse method has entering in system exclusive mode.  
    This method can be used in combination with the IsByteCaptured method to process sysex flows
    on the fly, without buffering.    

#### bool isSysExError()

    Return true if the last call to the parse method has detected an abnormal end of sysex,
    generally no 0xF7 end of sysex (EOX) byte.
  
#### bool isByteCaptured()
    
    Return true if the last byte parsed was kept to prepare a midi message.

#### uint8_t getMidiMsgType()

    Return the type of the last parsed midi message. 
    
    Types are defined by the following enumeration :
    . midiXparser::noneMsgType
    . midiXparser::channelVoiceMsgType
    . midiXparser::systemCommonMsgType
    . midiXparser::realTimeMsgType
    . midiXparser::sysExMsgType
    
#### uint8_t getMidiStatusMsgType(uint8_t midiStatus)
    
    Return the standard length of a midi status. Midi status are defined 
    by the following enumaration :
    
    CHANNEL VOICE
    . midiXparser::noteOffStatus
    . midiXparser::noteOnStatus
    . midiXparser::polyKeyPressureStatus
    . midiXparser::controlChangeStatus
    . midiXparser::programChangeStatus
    . midiXparser::channelPressureStatus
    . midiXparser::pitchBendStatus
    
    SYSTEM COMMON
    . midiXparser::soxStatus
    . midiXparser::midiTimeCodeStatus
    . midiXparser::songPosPointerStatus
    . midiXparser::songSelectStatus
    . midiXparser::reserved1Status
    . midiXparser::reserved2Status
    . midiXparser::tuneRequestStatus
    . midiXparser::eoxStatus
    
    REAL TIME
     . midiXparser::timingClockStatus
     . midiXparser::reserved3Status
     . midiXparser::startStatus
     . midiXparser::continueStatus
     . midiXparser::stopStatus
     . midiXparser::reserved4Status
     . midiXparser::activeSensingStatus
     . midiXparser::systemResetStatus
    

    uint8_t     getMidiStatusMsgLen(uint8_t midiStatus);
    uint8_t *   getMidiMsg();
    uint8_t *   getSysExMsg();
    byte        getByte() ;
    byte        getPreviousByte() ;
    unsigned    getSysExMsgLen() ;
    void        setMidiChannelFilter(uint8_t midiChannelFilter);
    void        setChannelVoiceMsgFilter(uint8_t channelVoiceMsgFilterMask);
    void        setSystemCommonMsgFilter(uint8_t systemCommonMsgFilterMask);
    void        setRealTimeMsgFilter(uint8_t realTimeMsgFilterMask);
    void        setMidiMsgFilter(allNoValues value);

    bool        setSysExFilter(bool sysExFilterToggle,unsigned sysExBufferSize=0);

    bool        parse(byte readByte);


