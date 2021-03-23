<h1 align="center"> Status Report </h1> <br>

<p align="center">
  Current Issues in the implementation of Okapi Segmentations
</p>



## Sample Input text file

```
Dear all,

This is for testing purpose. Kind request to ignore.

Please forgive me. I'm very Sorry. I Apologise. So sweet of you Thanks. Thank you. Great. Superb.

Regards,
Warm Regards,
Warmest Regards,

Sridhar Ram
```


## Current Approach
1.	Using Okapi Framework, Set of File filters are being used in the implementation for File Formatting to extract translatable text from the input text source (e.g. Sample.txt, sample.docx. sample.html, etc. (16 file formats are in the development process as of now), Simple ".txt" format is chosen as of now).
2.	Extracted translatable content from the files using File filter (PlainTextFilter) is required to be segmented as sentence-wise. 
    For example, When the document consists of set of paragraphs, each paragraph would consist number of sentences. Here, in the extraction process, we need the sentences to be segmented in order to reduce the complexity on workspace.
3.	While working with Rainbow tool, it seems, it has been designed to use SRX Segmentary [SRXSegmenter] for segmenting each sentence from the input source/document (i.e. file content). Segmentation rules is required for SRXSegmenter. So, we've taken a Default SRX Rules from the Rainbow tool for sentence-wise segmentation (which is tested using Ratel tool as well - by applying in regex patterns) from Okapi framework. 
4.	The results are verified in the Ratel tool UI output for the given Regex pattern. An SRX file is hence created post verification.

##### "SegmentationRules.srx" file content as follows,
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- LGPL see http://languagetool.cvs.sourceforge.net/viewvc/languagetool/JLanguageTool/src/resource/segment.srx -->
<srx xmlns="http://www.lisa.org/srx20" xmlns:okpsrx="http://okapi.sf.net/srx-extensions"
	version="2.0">
	<header segmentsubflows="yes" cascade="yes">
		<formathandle type="start" include="no"></formathandle>
		<formathandle type="end" include="yes"></formathandle>
		<formathandle type="isolated" include="no"></formathandle>
		<okpsrx:options oneSegmentIncludesAll="no"
			trimLeadingWhitespaces="no" trimTrailingWhitespaces="yes"></okpsrx:options>
		<okpsrx:sample language="en" useMappedRules="yes">Mr. Holmes is from the U.K. not the U.S. Is Dr. Watson from there too?</okpsrx:sample>
		<okpsrx:rangeRule></okpsrx:rangeRule>
	</header>
	<body>
		<languagerules>
			<languagerule languagerulename="default">
				<rule break="no">
					<beforebreak>\b(Sun|Mon|Tue|Tues|Wed|Weds|Thu|Thur|Thurs|Fri|Sat|Jan|Feb|Mar|Apr|Jun|Jul|Aug|Sep|Sept|Oct|Nov|Dec|Pr|Pres|ie|Inc|Ltd|Corp|max|min|pp|sel|ed|no|DC|comp|Gen|Ex|Lev|Num|Deut|Josh|Judg|Neh|Esth|Ps|Prov|Eccl|Isa|Jer|Lam|Ezek|Dan|Obad|Hab|Zeph|Hag|Zech|Mal|Matt|Rom|Cor|Gal|Eph|Philip|Col|Thes|Tim|Philem|Heb|Pet|Jn|Rev|Ne|Hel|Morm|Moro|Abr|Sam|Kgs|Chr|US|www|lds|pm|am|No|Mar|pp|e\.?\s*g|i\.?\s*e|no|[Vv]ol|[Rr]col|maj|Lt|[Ff]ig|[Ff]igs|[Vv]iz|[Vv]ols|[Aa]pprox|[Ii]ncl|[Dd]ept|min|max|[Gg]ovt|c\.?\s*f|vs)\.</beforebreak>
					<afterbreak>\s[^\p{Lu}]</afterbreak>
				</rule>
				<rule break="no">
					<beforebreak>\b(St|Gen|Hon|Dr|Mr|Ms|Mrs|Col|Maj|Brig|Sgt|Capt|Cmnd|Sen|Rev|Rep|Revd|A|B|C|D|Dr|E|F|G|H|I|J|K|L|M|N|O|P|Pr|Pres|Q|R|S|T|U|V|W|X|Y|Z|p|Ibid|comp)\.</beforebreak>
					<afterbreak>\s</afterbreak>
				</rule>
				<rule break="no">
					<beforebreak>([A-Z]\.){2,}</beforebreak>
					<afterbreak>\s\p{Lu}</afterbreak>
				</rule>
				<rule break="no">
					<beforebreak>([0-9]+\.[0-9]+|[0-9]+\.[0-9]*|[0-9]*\.[0-9]+)</beforebreak>
					<afterbreak></afterbreak>
				</rule>
				<rule break="no">
					<beforebreak>^\s+[0-9]+\.</beforebreak>
					<afterbreak>\s\p{Lu}</afterbreak>
				</rule>
				<rule break="no">
					<beforebreak>\.\s*\.\s*\.'</beforebreak>
					<afterbreak></afterbreak>
				</rule>
				<rule break="yes">
					<beforebreak>[\p{Ll}\p{Lu}\p{Lt}\p{Lo}\p{Nd}]+[\p{Pe}\p{Po}"]*[\.?!]+[\p{Pe}\p{Po}"]*</beforebreak>
					<afterbreak>\s</afterbreak>
				</rule>
				<rule break="yes">
					<beforebreak>\n</beforebreak>
					<afterbreak></afterbreak>
				</rule>
			</languagerule>
			<languagerule languagerulename="cjk">
				<rule break="yes">
					<beforebreak>[\u3002\ufe52\uff0e\uff61\u2049\ufe56\uff1f\u203c\u2048\u2762\u2763\ufe57\uff01]+</beforebreak>
					<afterbreak></afterbreak>
				</rule>
			</languagerule>

			<languagerule languagerulename="thai">
				<rule break="yes">
					<!-- Only break on whitesapce between Thai characters and only if the 
						segment is at least 30 characters -->
					<beforebreak>[\u0e01-\u0e5b]{30,}</beforebreak>
					<afterbreak>\s+</afterbreak>
				</rule>
			</languagerule>

			<languagerule languagerulename="khmer">
				<rule break="yes">
					<beforebreak>[\u17D4\u17D5]</beforebreak>
					<afterbreak>\s+</afterbreak>
				</rule>
			</languagerule>

		</languagerules>
		<maprules>
			<languagemap languagepattern="[Jj][Aa].*"
				languagerulename="cjk"></languagemap>
			<languagemap languagepattern="[Zz][Hh].*"
				languagerulename="cjk"></languagemap>
			<languagemap languagepattern="[Kk][Oo].*"
				languagerulename="cjk"></languagemap>
			<languagemap languagepattern="[Tt][Hh].*"
				languagerulename="thai"></languagemap>
			<languagemap languagepattern="[Kk]([Hh]?)[Mm].*"
				languagerulename="khmer"></languagemap>
			<languagemap languagepattern=".*" languagerulename="default"></languagemap>
		</maprules>
	</body>
</srx>
```


### mainProcessor.java file as follows,
```java
package com.ailaysa.translate.processor.impl;

import com.ailaysa.translate.model.Document;
import com.ailaysa.translate.model.TextPair;
import net.sf.okapi.common.Event;
import net.sf.okapi.common.EventType;
import net.sf.okapi.common.IResource;
import net.sf.okapi.common.LocaleId;
import net.sf.okapi.common.filters.IFilter;
import net.sf.okapi.common.filterwriter.IFilterWriter;
import net.sf.okapi.common.filterwriter.TMXWriter;
import net.sf.okapi.tm.pensieve.seeker.ITmSeeker;
import net.sf.okapi.tm.pensieve.seeker.PensieveSeeker;
import net.sf.okapi.tm.pensieve.seeker.TmSeekerFactory;
import net.sf.okapi.common.resource.*;
import net.sf.okapi.filters.html.HtmlFilter;
import net.sf.okapi.filters.plaintext.PlainTextFilter;
import net.sf.okapi.steps.wordcount.WordCounter;
import net.sf.okapi.tm.pensieve.tmx.*;
import net.sf.okapi.lib.segmentation.SRXDocument;
import net.sf.okapi.common.*;
import org.apache.commons.lang3.StringUtils;

import java.io.File;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import net.sf.okapi.steps.common.RawDocumentToFilterEventsStep;


//maven for filters =>  https://mvnrepository.com/artifact/net.sf.okapi.filters

public class mainProcessor {

    public String MARKERS_REGEX;  //;= "[\uE101-\uE120]";
    public IFilter filter;
    public File file;
    public Document document;
    public File inFile;
    public String extension;

    public mainProcessor(String MARKERS_REGEX, IFilter filter, File file) {
    
        /*for parse function  use this */
        this.MARKERS_REGEX = MARKERS_REGEX;
        this.file = file;
        this.filter = filter;
        
    }

    public mainProcessor(String MARKERS_REGEX, IFilter filter, File file, Document document, File inFile, String extension) {
    
        /*for translate use this */
        this(MARKERS_REGEX, filter, inFile);
        this.document = document;
        this.inFile = inFile;
        this.extension = extension;
        
    }

    public static void main(String[] args){
    
        mainProcessor MainProcessor = new mainProcessor("[\uE101-\uE120]" , new PlainTextFilter(), new File("D:\\Sridhar Workspace\\Ailaysa.io\\Test Data\\Input Files\\English\\Sample 1\\source2.txt") );
        MainProcessor.parse();
        
    }

    //    @Override
    public Document parse(  ) {
    
         SRXDocument doc = new SRXDocument();
         File f = new File("C:\\Users\\Hp\\Desktop\\SegmentationRules.srx"); // SegmentationRules.srx has been taken here
         doc.loadRules(f.getAbsolutePath());

    //  Obtain a segmenter for English
        ISegmenter segmenter = doc.compileLanguageRules(LocaleId.fromString("en"), null);

        if (file == null)
            throw new IllegalArgumentException();
            
        Document document = new Document();
        long numberOfWords = 0l;
        Map<String, List<TextPair>> textMap = new LinkedHashMap<>();
        IFilter filter = new PlainTextFilter();

        filter.open(new RawDocument(file.toURI(), StandardCharsets.UTF_8.name(), LocaleId.US_ENGLISH, LocaleId.FRENCH));

        while (filter.hasNext()) {
        
            Event event = filter.next();
            System.out.println("event===>" +event.toString());
            EventType eventType = event.getEventType();
            
            switch (eventType) {
            
                case TEXT_UNIT:
                
                    TextUnit tu = (TextUnit) event.getResource();
                    String id = tu.getId();
                    
                    TextContainer tc = tu.createTarget(LocaleId.ENGLISH, false, IResource.COPY_ALL);
                     segmenter.computeSegments(tc);
                     
                    ISegments segs = tc.getSegments();
//                    System.out.println(segs);

                    for (Segment seg : segs) {
                    
                        TextFragment tf = seg.getContent();
                        String text = tf.getCodedText();
                        System.out.println(text);
                        
                        long wordsInString = WordCounter.count(text, LocaleId.ENGLISH);
                        
                        if (wordsInString > 0) {
                        
                            numberOfWords = numberOfWords + wordsInString;
                            String[] tokens = text.split(MARKERS_REGEX);
                            
                            for (String s : tokens) {
                            
                                if (StringUtils.isNotBlank(s) && WordCounter.count(s, LocaleId.ENGLISH) > 0) {
                                
                                    TextPair textPair = new TextPair();
                                    textPair.setOriginal(s);
                                    List<TextPair> list = textMap.containsKey(id) ? textMap.get(id) : new ArrayList<>();
                                    list.add(textPair);
                                    textMap.put(id, list);
                                    
                                }
                            }
                        }
                    }
            }
        }
        
        filter.close();

        document.setFilename(file.getName());
        document.setNumberOfWords(numberOfWords);
        document.setText(textMap);
        return document;
        
    }


    //    @Override
    public void translate() {
    
        if (inFile == null)
            throw new IllegalArgumentException();

        Map<String, List<TextPair>> textMap = document.getText();
//         System.out.println( document );

        String outputFilename = inFile.getAbsolutePath().replace(extension, ".out" + extension);
        System.out.println(outputFilename);

//        IFilter filter = new PlainTextFilter();
        filter.open(new RawDocument(inFile.toURI(), StandardCharsets.UTF_8.name(), LocaleId.US_ENGLISH , LocaleId.FRENCH));
        IFilterWriter writer = filter.createFilterWriter();
        
        writer.setOutput(outputFilename);
        writer.setOptions(LocaleId.ENGLISH , StandardCharsets.UTF_8.name());

        while (filter.hasNext()) {
        
            Event event = filter.next();
            EventType eventType = event.getEventType();
            
            switch (eventType) {
            
                case TEXT_UNIT:
                
                    TextUnit tu = (TextUnit) event.getResource();
                    String id = tu.getId();

                    if (textMap.containsKey( id)) {
                    
                        int index = 0;
                        List<TextPair> list = textMap.get( id);
                        TextContainer tc = tu.createTarget(LocaleId.ENGLISH , false, IResource.COPY_ALL);
                        ISegments segs = tc.getSegments();
                        
                        for (Segment seg : segs) {
                        
                            TextFragment tf = seg.getContent();
                            String fragmentText = tf.getCodedText();
                            long wordsInString = WordCounter.count(fragmentText, LocaleId.ENGLISH);
                            
                            if (wordsInString > 0) {
                            
                                for (int i = index; i < list.size(); i++) {
                                
                                    TextPair pair = list.get(i);
                                    String ot = pair.getOriginal();
                                    
                                    if (fragmentText.contains(ot)) {
                                    
                                        String tt = pair.getTranslate();
                                        if (StringUtils.isNotBlank(tt)){
                                          fragmentText = fragmentText.replace(ot, tt);
                                        } else  {
                                          fragmentText = fragmentText.replace(ot, " ");
                                        }
                                        index = i;
                                    }
                                }
                            }
                            
                            tf.setCodedText(fragmentText);
                        }
                    }
            }
            writer.handleEvent(event);
        }

        filter.close();
        writer.close();
    }
}

```

### Output While Running mainProcessor.java
![mainProcessor](https://user-images.githubusercontent.com/15103613/112156041-e5087e00-8c0b-11eb-82e1-d488abf8fa46.png)

From the above process, we’re about to take the Text content from the input file (i.e. source2.txt) are being extracted by Paragraph instead of sentences. 


### Ratel Tool from Okapi Framework
But on the other hand, when we’re testing Ratel tool, segmentation process happens as follows (using the same SRX Rules - SegmentationRules.srx).
![Ratel_Sentencewise_Segmentation](https://user-images.githubusercontent.com/15103613/112156410-39abf900-8c0c-11eb-9b69-03fd4697bf2a.png)


## Quick Start for Guidance Requirement
We ensure the tools provided by Okapi Framework for Text Filters, Sentence Segmentation, Text Extraction, Translation using Google Machine Translation Engine (v2, paid services), Translated Content Merging and Translated File Output.

1.	We’d like to let our End-users to do translations sentence-wise segmented text instead of Hugh Paragraphs. And the method we have tried are as follows.
i)    [Using IFilter](https://okapiframework.org/javadoc/net/sf/okapi/common/filters/IFilter.html)
ii)   [Reading Document using Okapi Framework](https://okapiframework.org/devguide/gettingstarted.html#readingDocument)
iii)  [Performing Segmentation](https://okapiframework.org/devguide/segmentation.html#performingSegmentation)

2.	Also we would like to know whether we have any other possibilities to achieve this sentence-wise segmentation using other methods rather using Okapi Framework.
