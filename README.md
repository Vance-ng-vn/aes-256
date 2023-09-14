# aes-256
aes-256 simple encrypt on php and decrypt on C#

#PHP:
```
function encryptString($plainText, $key) {
    $method = "aes-256-cbc";
    $aesKey = utf8_encode($key);
    $aesKey = str_pad($aesKey, 32, "\x00");
    $iv = str_repeat("\x00", 16);
    $encrypter = openssl_encrypt($plainText, $method, $aesKey, $option=0, $iv);
    return $encrypter;
}
```
#CS:
```
private static string DecryptString(string encryptedText, string aesKey)
{
    byte[] aesKeyBytes = Encoding.UTF8.GetBytes(PadKey(aesKey));
    byte[] iv = new byte[16]; // IV cố định là 16 byte 0

    using (Aes aesAlg = Aes.Create())
    {
        aesAlg.Key = aesKeyBytes;
        aesAlg.IV = iv;
        aesAlg.Mode = CipherMode.CBC; // Sử dụng chế độ CBC để giống với PHP

        ICryptoTransform decryptor = aesAlg.CreateDecryptor(aesAlg.Key, aesAlg.IV);

        byte[] cipherText = Convert.FromBase64String(encryptedText);

        using (MemoryStream msDecrypt = new MemoryStream(cipherText))
        using (CryptoStream csDecrypt = new CryptoStream(msDecrypt, decryptor, CryptoStreamMode.Read))
        using (StreamReader srDecrypt = new StreamReader(csDecrypt))
        {
            return srDecrypt.ReadToEnd();
        }
    }
}
private static string PadKey(string key)
{
    if (key.Length < 32)
    {
        key = key.PadRight(32, '\0');
    }
    else if (key.Length > 32)
    {
        key = key.Substring(0, 32);
    }
    return key;
}
```
