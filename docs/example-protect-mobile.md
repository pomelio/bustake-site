
# Protect Your Mobile Number

This voice app is used to protect your personal mobile number. You can buy a local landline number from Twillio website. You can link the following `bus script` with the number. When an inbound call comes and hit this script, it firstly checks the current time to see whether is out of hours. If not, it will ask  the caller to enter 1 if the caller is a job agent or 2 if the caller is looking for building IVR, then the app derverts the call to the mobile number, when the mobile number is answered, it will play a `telling message` to remaind the receiver is `job agent` or  `ivr works`. If it is out of hours, it will play an message to tell the caller to call again between 9am to 5pm then hang up.

The github [link] (https://gist.github.com/Benbus/f31bc6c624d747c932df4b8da2438151)

```bus
// version=1, timezone='America/Los_Angeles', voice='Polly.Russell'

if HOUR < 9 || HOUR > 16 {

    after_hours =`
(1s)
Sorry I am Ben.(200ms)
I am not available now.
Please call me between [9 am to 5 pm ](emphasis level="strong")
Bye Bye.
`;
    audio name='after_hours', tts=after_hours;
    play_audio ref='after_hours';
    hang_up;
}

main_menu_tts =`
(500ms)
Hello I am Ben.

Press 1 (200ms) if you are a [job agen](emphasis level="strong")
Press 2 (200ms) if you are looking for building a [voice App](emphasis level="strong")
`;

audio name='main_menu', tts=main_menu_tts;
audio name='no_input', tts='no input detected.';
audio name='no_match', tts='wrong option entered.';
audio name='wrong_option', tts='wrong option entered, bye bye.';
ivr name='main_menu', audioRef='main_menu', audioNoInputRef='no_input', audioNoMatchRef='no_match', numDigits=1;
play_ivr ref='main_menu', noInputCount=2, noMatchCount=2, timeout='10';

audio name='calling_wait', tts='please wait, connecting Ben.';

switch GATHER_DIGITS {
    case '1' {
       play_audio ref='calling_wait';
       dial value='1223333';
    }
    case '2' {
        play_audio ref='calling_wait';
        dial value='1223333';
    }
    case '' {
        play_audio ref='no_input';
        hang_up;
    }
    default {
        play_audio ref='wrong_option';
        hang_up;
    }
}


```

- line 1, the first line must be a comment line with `version`, `timezone` and `default voice`. The timezone will be used to caculate the local time, the `voice` is used to the `text to speech`.
- lines  3 - 15 are `if statement`
- line 3 checks the `HOUR built-in object`, this is the local time in terms of the `timezeon` above
- lines 5 - 15 are executed when the `HOUR` object value is between 9 am to 5 pm
- lines 5  - lines 11 are an `assignment statement` of ` string object` the value is from a `tts`
- line 6 is a slicence in 1 second
- line 7 ends with 200 milli seconds
- line 12 is an `audio statement` creates an `audio object`. The `tts` value is from the above string value
- line 13 is a `play_audio statement` which plays the `audio object`
- line 14 is a `hang up` statment which hang up the current call. The statements afterwards won't be executed.
- line 17 - 23 is a `assignment statement` creates a `string object` the value is `tts` which asks the caller to enter 1 if it is a job agent or 2 is a voice app work.
- line 25 is an `audio statement` which is played the main menu message of ivr
- line 26 is an `audio statement` which is played when no input detected of ivr
- line 27 is an `audio statement` which is played when wrong option is entered of ivr
- line 28 is an `ivr statement` creates an `ivr object` with the name, with the main menu aduio object, no input audio object and number of digit entered expected.
- line 30 is a `play_ivr statement` plays the above `ivr object`, the ivr will be played max 2 loops, and timeout in 10 seconds
- line 32 is an `audio statement` creates an audio object.
- line 34 - 54 is an `switch statement`
- line 34 is the begining of the `switch statement` evaluate the `built-in object GATHER_DIGITS`, this is an `string object`, the value is from the above inputs.
- lines 35 - 39 are executed when the value of `object GATHER_DIGITS` is `'1'`
- line 36 is a `play_audio statement` plays above `audio object` with the name `calling_wait`
- line 37 is a `assignment statement` creates a `string object`, the value of the `string object` is an url which points to a `handlebars` template. The value is returned from the `built-in function` template_url_encode
- line 38 is a `dial statement` creates an outbound call with the number, when the number is anwered, an TwiXML is played to the reeiver before the outbound call is connected.
- lines 40 - 44 are executed when the value of `object GATHER_DIGITS` is `'2'`
- lines 45 - 48 are executed when the value of `object GATHER_DIGITS` is empty which means no inputs entered in above ivr.
- lines 49 - 52 are executed when the inputs value is not `'1'` or `'2'`.
