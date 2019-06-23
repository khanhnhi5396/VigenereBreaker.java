# VigenereBreaker.java
Duke Java Programming: Arrays, Lists, and Structured Data (Week 4)
import java.util.*;
import edu.duke.*;
import java.io.*;

/* From DUKE university, on Coursera
 * Java Programming: Arrays, Lists, and Structured Data (Week 4)
 * author: khanhnhi5396 
*/

public class VigenereBreaker {
  
    public String sliceString(String message, int whichSlice, int totalSlices) {
        StringBuilder s = new StringBuilder();
        for ( int i = whichSlice; i< message.length() ; i+= totalSlices) {
            s.append(message.charAt(i));
        }
        return s.toString();
    }
    
     public void testerSliceString (){
       String ans = (sliceString("abcdefghijklm", 0, 3)); 
       System.out.println(ans);
       String ans1 = (sliceString("abcdefghijklm", 1, 3)); 
       System.out.println(ans1);
       String ans2 = (sliceString("abcdefghijklm", 2, 3)); 
       System.out.println(ans2);
       String ans3 = (sliceString("abcdefghijklm", 0, 4)); 
       System.out.println(ans3);
       String ans4 = (sliceString("abcdefghijklm", 1, 4)); 
       System.out.println(ans4);
       String ans5 = (sliceString("abcdefghijklm", 2, 4)); 
       System.out.println(ans5);
       String ans6 = (sliceString("abcdefghijklm", 3, 4)); 
       System.out.println(ans6);
       String ans7 = (sliceString("abcdefghijklm", 0, 5)); 
       System.out.println(ans7);
       String ans8 = (sliceString("abcdefghijklm", 1, 5)); 
       System.out.println(ans8);
       String ans9 = (sliceString("abcdefghijklm", 2, 5)); 
       System.out.println(ans9);
       String ans10 = (sliceString("abcdefghijklm", 3, 5)); 
       System.out.println(ans10);
       String ans11 = (sliceString("abcdefghijklm", 4, 5)); 
       System.out.println(ans11);
       
}
    public int[] tryKeyLength(String encrypted, int klength, char mostCommon) {
        int[] key = new int[klength];
        
        
        for(int i=0; i<klength ; i++){
            String slice = sliceString(encrypted, i,klength);
            CaesarCracker cc = new CaesarCracker('e');
            key[i] = cc.getKey(slice);
            
        }
        
        return key;
    }

    public void testTryKeyLength(){
      VigenereBreaker vb = new VigenereBreaker();
        FileResource fr = new FileResource();
        int[] m = vb.tryKeyLength(fr.asString(), 4, 'e');
        for (int i = 0; i < m.length; i++) {
            System.out.println(m[i]);
        }
        VigenereCipher vc = new VigenereCipher(m);
        System.out.println(vc.decrypt(fr.asString())); //work good!!! :)
    
      
    }
    
    public void breakVigenere () {
        FileResource fr = new FileResource();
        String msg = fr.asString();
        int klength =4 ;
        int[] keyList = tryKeyLength(msg, klength , 'e'); 
        
        VigenereCipher vc = new VigenereCipher(keyList);
        
        System.out.print(vc.decrypt(msg));
        
    }
    
    public void anotherTest(){
     String a = "Hhdiu LVXNEW uxh WKWVCEW, krg k wbbsqa si Mmwcjiqm";
     int[] key = {3,20,10,4};
     VigenereCipher vc = new VigenereCipher(key);
     System.out.print(vc.decrypt(a));
        
    }
    
    public HashSet<String> readDictionary(FileResource fr){
       HashSet<String> hs = new HashSet<String>();
       
       for(String each : fr.lines()){
        
         hs.add(each.toLowerCase());
           
        }
        return hs;
    }
    
    public int countWords(String message, HashSet<String> dictionary){
     int count = 0;
          
     for(String word : message.split("\\W+")){
         
         if(dictionary.contains(word.toLowerCase())){
             count ++;
            }
         
        }
      return count;   
    }
    
    public String breakForLanguage(String encrypted, HashSet<String> dictionary){
     String decryptedMess = null;
       
     int keyLength = 0;
     int max = 0;
     char mostCommonChar = mostCommonCharIn(dictionary);
     
        for(int i=1;i<=100;i++){
            int [] keys = tryKeyLength(encrypted, i, mostCommonChar);
            VigenereCipher vc = new VigenereCipher(keys);
           
            String decryptedMessage = vc.decrypt(encrypted);
            int cw = countWords(decryptedMessage,dictionary);
            
            if(cw > max){
               max = cw;
               keyLength = i;
               decryptedMess = decryptedMessage;
            }
        }
       
        System.out.println(" key length is " + keyLength);
        int[] trueKey = tryKeyLength(encrypted,keyLength,mostCommonChar);
        for(int k =0;k< trueKey.length;k++){
            System.out.println(trueKey[k]);
        }
        
       return decryptedMess;
    }
    
    public void breakVigenere2(){
     FileResource fr = new FileResource();
     String mess = fr.asString().toLowerCase();
     
     FileResource fr1 = new FileResource("dictionaries/English");
     HashSet<String> dictionary = readDictionary(fr1);
     
     String decrypt = breakForLanguage(mess,dictionary);
     
     
     System.out.print(decrypt);
     System.out.println("Valid words are in the decrypted String is " + countWords(decrypt,dictionary));
     
     String dec38 = new VigenereCipher(tryKeyLength(mess,38,'e')).decrypt(mess);
     System.out.println("Valid words ( 38 ) is " + countWords(dec38,dictionary));
     
     
    }
    
    public char mostCommonCharIn(HashSet<String> dictionary){
       HashMap<Character,Integer> counts = new HashMap<>();
        for (String word : dictionary){
            for (int i = 0; i < word.length();i++){
                char currCh = word.charAt(i);
                if ( ! counts.containsKey(currCh)){
                    counts.put(currCh,1);
                } else {
                    counts.put(currCh,counts.get(currCh) + 1);
                }
            }
        }
    int max = 0;
    char mostCommonChar = ' ';
    for(char s: counts.keySet()){
        if(counts.get(s) > max){
            max = counts.get(s);
            mostCommonChar = s;
        }
    }
    
    return mostCommonChar;
}

public String breakForAllLangs (String encrypted, HashMap <String, HashSet<String>> languages){
    /*int count = 0;
    String language = null; 
    for(String s : languages.keySet()){
     int countWords = countWords(encrypted, languages.get(s));
     if(countWords > count){
         count = countWords;
          language = s;
        }
       
    }
    
    System.out.println("Language is " + language);
    
    String ans = breakForLanguage(encrypted, languages.get(language));
    
    return ans;*/
    int maxCount =0;
    	String potentialDec= "";
    	String language = "";
    	for(String each : languages.keySet()) {
    		System.out.println("language : " + each);
    		HashSet<String> dictionary = languages.get(each);
			String decrypted = breakForLanguage(encrypted, dictionary);
			int count = countWords(decrypted, dictionary);
			if(count > maxCount) {
				maxCount = count;
				potentialDec = decrypted; 
				language = each;
			}
    	}
    	System.out.println("language : " + language);
    	System.out.println("decrypted : \n" + potentialDec);
    return potentialDec;
}

public void breakVigenere3(){
   
    FileResource text = new FileResource("secretmessage4.txt"); 
    FileResource dic;
         
		VigenereBreaker vb=new VigenereBreaker();
		String encrypted=text.asString();

		HashMap<String,HashSet<String>> language =new HashMap<String,HashSet<String>>();
		DirectoryResource dr=new DirectoryResource();
		for(File f:dr.selectedFiles()){
			String filename=f.getName();
			FileResource fr1=new FileResource(f);
			HashSet<String> dictionary= vb.readDictionary(fr1);
			language.put(filename, dictionary);
		}
		
		System.out.println(vb.breakForAllLangs(encrypted,language));
		
}

}
