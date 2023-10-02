---
layout: site
title: Writing a BEAST 2.7 Package 
tags: []
---

	
		
The goal of this tutorial is to illustrate how to create an package for BEAST 2. You will create an package that adds a new nucleotide substitution (F84) model to BEAST 2. The tutorial assumes that the reader is using MacOS 12.5, but the tutorial should be reasonably portable. This tutorial was created by the &#8220;Add-On Tutorial and SDK&#8221; breakout group at the third NESCent BEAST meeting, Dec. 5-8, 2011, and revised at the fourth NESCent BEAST meeting, Dec. 3-6, 2013. Please contact <a class="external text" href="mailto:paul.lewis@uconn.edu" rel="nofollow">Paul Lewis</a>, <a class="external text" href="mailto:beerli@fsu.edu" rel="nofollow">Peter Beerli</a>, or <a class="external text" href="mailto:r.bouckaert@auckland.ac.nz" rel="nofollow">Remco Bouckaert</a> with comments/suggestions.

## Installing Eclipse

We will use the Eclipse IDE to manage the new package project, so the first step is to install Eclipse if you do not already have it on your system. Go to <a class="external free" href="http://www.eclipse.org/downloads/" rel="nofollow">http://www.eclipse.org/downloads/</a>and download &#8220;Eclipse IDE for Java Developers&#8221; for your system. For our MacOS 10.8.5 system, we unzipped the downloaded <i>eclipse-jee-kepler-SR1-macosx-cocoa-x86_64.tar.gz</i> file by double-clicking it, then moved the unpacked folder (named <i>eclipse</i>) to our Applications folder. You may wish to drag <i>Eclipse.app</i> to your dock to make it easy to open. On opening Eclipse for the first time, you will be asked to select a workspace. For the purpose of this tutorial, we will assume you chose this directory:

{% highlight xml %}$HOME/Documents/workspace{% endhighlight %}

## Downloading JDK

BEAST is java based and uses the JavaFX libraries but no modules. It is easiest to use the JDK from Azul that includes the JavaFX. Download it from <a href="https://www.azul.com/downloads/?version=java-17-lts&package=jdk-fx">here</a> for your operating system and uncompress the file.

## Setting up JDK in Eclipse

To make the JDK available in Eclipse, select menu <b>Eclipse > Preferences</b>, type <b>jre</b> in the filter and select <b>Installed JREs</b> in the list. Now follow the following steps

* Click the <b>Add</b> button to add the Azul JDK.
* Click the <b>Next</b> button to add a standard VM
* Click the <b>Directory...</b> button
* Navigate to the directory where you uncompressed the JDK, and select the directory and click <b>Open</b></li>
* Select the new JDK as default
* Click the <b>Finish</b> button

## Downloading the BEAST 2 source

Now, open Terminal app (in the MacOS Utilities folder) and navigate to the Eclipse workspace directory by typing this command:

{% highlight xml %}cd $HOME/Documents/workspace{% endhighlight %}			

Assuming git is installed, you should be able to download the BEAST 2 source code using these commands:

{% highlight xml %}
git clone https://github.com/CompEvol/beast2.git
git clone https://github.com/CompEvol/BeastFX.git			{% endhighlight %}

## Creating a Beast 2 project

Back in Eclipse, use <b>File > New > Project&#8230; > Java project</b> to create a new Java project named &#8220;beast2&#8221;. 
Make sure the <b>Create module-info.java</b> box is unchecked.
You should be able to leave everything in the <b>New Java Project</b> dialog box at its default value. 
Once you press the <b>Finish</b> button, Eclipse will proceed to compile &#8220;beast2&#8221;.

## Creating a BeastFX project

BeastFX contains all applications, including BEAUti and BEAST, which you want to use for testing and running models from your package, so we set it up as well.

Use <b>File > New > Project&#8230; > Java project</b> to create a new Java project named &#8220;BeastFX&#8221;. Click <b>Next</b> and go to the <b>Project</b> tab. 

Go to the <b>Projects</b> tab. Select <b>Classpath</b> then click the <b>Add</b> button. Select <b>beast2</b> from the list, and click <b>OK</b>.

Select the <b>Classpath</b>, then click the <b>Add Jars...</b> button. 
Under the <b>beast2/lib</b> folder, select <b>antlr-runtime</b>, <b>beagle</b>, <b>colt</b>, and <b>commons-math</b> and under <b>junit</b> select <b>junit-platform...</b>. Click the <b>OK</b> button

Click the <b>Finish</b> button and Eclipse will compile the BeastFX project.

## Creating a project for &#8220;MyPackage&#8221;

Now you need to create an Eclipse project for your package and make it depend on the &#8220;beast2&#8221; anv &#8220;BeastFX&#8221; projects.

o Use <b>File > New > Java project</b> to create a new Java project named &#8220;MyPackage&#8221;. Again leave everything in the <b>New Java Project</b> dialog box at its default setting and press the <b>Finish</b> button. 

o Now you should see three projects in the <b>Project Explorer</b> pane: &#8220;beast2&#8221; &#8220;BeastFX&#8221; and &#8220;MyPackage&#8221;. If you see <b>Package Explorer</b> rather than <b>Project Explorer</b>, use <b>Window > Show View > Project Explorer</b> to bring up the <b>Project Explorer</b> view instead.

o Right-click (or Control-click if you do not have a mouse with two buttons) &#8220;MyPackage&#8221; and choose <b>Properties</b> from the speed menu that appears.

o Choose <b>Java Build Path</b>, click the <b>Projects</b> tab, click the <b>Add&#8230;</b> button, then check &#8220;beast2&#8221; and &#8220;BeastFX&#8221; and press the <b>OK</b> button. Now press the <b>OK</b> button in the <b>Properties for MyPackage</b> dialog box to close it.

## Creating a package for &#8220;MyPackage&#8221;

With &#8220;MyPackage&#8221; selected in the Project Explorer, use <b>File > New > Package</b> to create a package. The <b>Source folder</b> for the package should be automatically filled in correctly (&#8220;MyPackage/src&#8221;), and the <b>name</b> should be set to &#8220;mypackage.evolution.substitutionmodel&#8221;. If Eclipse does not allow you to press the <b>Finish</b> button, cancel the dialog box, locate &#8220;mypackage.evolution.substitutionmodel&#8221; in the &#8220;beast2&#8221; project, copy it, then try again to create the package, this time pasting in &#8220;mypackage.evolution.substitutionmodel&#8221;.

## Creating an F84 class

Now right-click (or Control-click) on the &#8220;mypackage.evolution.substitutionmodel&#8221; package under &#8220;MyPackage&#8221; and choose <b>New > Class</b> from the resulting popup menu. Give the new class the name &#8220;F84&#8221; and press <b>Finish</b>. You should now see the <i>F84.java</i> file open in the editor, and it should contain the following text: package mypackage.evolution.substitutionmodel;

{% highlight xml %}public class F84 {

}{% endhighlight %}			

Add &#8220;extends SubstitutionModel.Base&#8221; following the class name to produce the following:

{% highlight xml %}package mypackage.evolution.substitutionmodel;
import beast.base.evolution.substitutionmodel.SubstitutionModel.Base;

public class F84 extends SubstitutionModel.Base {

}{% endhighlight %}			

At this point you should see an icon beside the class declaration. The icon consists of a light bulb with a red square containing a white letter X. Clicking this should bring up a popup menu that allows you to <b>Add unimplemented methods</b>. Your <i>F84.java</i> file should now look like this:

{% highlight xml %}package mypackage.evolution.substitutionmodel;

import beast.base.evolution.substitutionmodel.SubstitutionModel.Base;
import beast.base.evolution.substitutionmodel.EigenDecomposition;
import beast.base.evolution.datatype.DataType;
import beast.base.evolution.tree.Node;

public class F84 extends SubstitutionModel.Base {

	@Override
	public void getTransitionProbabilities(Node node, double fStartTime,
			double fEndTime, double fRate, double[] matrix) {
		// TODO Auto-generated method stub

	}

	@Override
	public EigenDecomposition getEigenDecomposition(Node node) {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public boolean canHandleDataType(DataType dataType) {
		// TODO Auto-generated method stub
		return false;
	}
}{% endhighlight %}			
			
		

Note: depending on your particular version of Eclipse (or some other as-yet-unidentified factor), the methods that appear may not be in the order listed above. These are methods that must be implemented because they are abstract methods in the <code>SubstitutionModel.Base</code> base class.

### Adding the k parameter

The F84 model comprises nucleotide frequency parameters as well as a parameter <i>k</i> that determines the number of extra transition-type substitutions (above and beyond those generated by the general substitution process). If <i>k</i> equals zero, then the F84 model collapses to the F81 model, which allows nucleotide composition to vary but does not allow transition/transversion bias. As <i>k</i> becomes larger, transitions become more common. The nucleotide frequencies are supplied by the base class, but the <i>k</i> parameter is unique to the F84 model and thus must be defined by our package class. To do this, add the following line to the class definition just after public class:

{% highlight xml %}F84 extends SubstitutionModel.Base {
   public Input<RealParameter> kF84Input = new Input<>("kF84", "k parameter in the F84 model", Validate.REQUIRED);
   .
   .
   .
}{% endhighlight %}			

Adding the <code>kF84</code> parameter will require you to insert some declarations at the top of the file. Eclipse helps you do this:

o First, save the file

o Click the light-bulb icon to get a popup menu, and double-click the first item in the popup menu: <b>Import &#8216;Input&#8217; (beast.core)</b>.

o Continue clicking the light-bulb icon until you have all three of these declarations inserted:

{% highlight xml %}import beast.base.core.Input;
import beast.base.core.Input.Validate;
import beast.base.inference.parameter.RealParameter;{% endhighlight %}			

### Adding the initAndValidate method

We must also override the <core>initAndValidate</core> method, within which a call to the super method allows initialization of the base class.

{% highlight xml %}   @Override
   public void initAndValidate() {
   	super.initAndValidate();
   	kF84Input.get().setBounds(0.0, Double.POSITIVE_INFINITY);
   }{% endhighlight %}			

### Implementing the canHandleDataType method

The F84 model is a substitution model applicable only to nucleotide sequences, so replace the body of the automatically-created stub with the following:

{% highlight xml %}@Override
public boolean canHandleDataType(DataType dataType) {
   if (dataType instanceof Nucleotide) {
     return true;
   }
   return false;
}{% endhighlight %}			

The method now returns true if dataType is an instance of the Nucleotide class, and otherwise throws an exception with an informative message. Adding this code requires you to insert the following declaration at the top of the file:

{% highlight xml %}import beast.base.evolution.datatype.Nucleotide;{% endhighlight %}			

### Implementing the getEigenDecomposition method

For now, leave this method unimplemented. We will return to it later after testing the basic implementation.

### Implementing the getTransitionProbabilities method

The getTransitionProbabilities method is expected to fill a 16 element vector with transition probabilities in the order AA, AC, AG, AT, CA, CC, CG, CT, GA, GC, GG, GT, TA, TC, TG, TT, where the first nucleotide in each pair is the &#8220;from&#8221; state and the second in each pair is the &#8220;to&#8221; state. Each transition probability depends on the edge length, which is measured in terms of the expected number of subsitutions. The expected number of substitutions is the product of rate (provided by <code>fRate</code>) and time (the difference between <code>fEndTime</code> and <code>fStartTime</code>). Note that time is viewed from the present looking toward the past, so <code>fStartTime - fEndTime</code> is the correct way to get the time difference. Fill in the <code>getTransitionProbabilities</code>method as follows:

{% highlight xml %}@Override
public void getTransitionProbabilities(Node node, double fStartTime, double fEndTime, double fRate, double[] matrix) {
       double[] freqs = frequencies.getFreqs();
       double freqA = freqs[0];
       double freqC = freqs[1];
       double freqG = freqs[2];
       double freqT = freqs[3];
       double freqR = freqA + freqG;
       double freqY = freqC + freqT;

       double k = kF84Input.get().getValue();
       double sumPiSquared = freqA*freqA + freqC*freqC + freqG*freqG + freqT*freqT;
       double sumPiRatios = freqA*freqA/freqR + freqC*freqC/freqY + freqG*freqG/freqR + freqT*freqT/freqY;
       double mu = (1.0 - sumPiSquared) + k*(1.0 - sumPiRatios);

       double distance = (fStartTime - fEndTime) * fRate;
       double expTerm = Math.exp(-mu * distance);
       double expTermK = Math.exp(-mu * distance * (k + 1.0));

       matrix[0]  = freqA + freqA*(1.0/freqR - 1.0)*expTerm + ((freqR - freqA)/freqR)*expTermK; //AA
       matrix[1]  = freqC*(1.0 - expTerm); //AC
       matrix[2]  = freqG + freqG*(1.0/freqR - 1.0)*expTerm - (freqG/freqR)*expTermK; //AG
       matrix[3]  = freqT*(1.0 - expTerm); //AT

       matrix[4]  = freqA*(1.0 - expTerm); //CA
       matrix[5]  = freqC + freqC*(1.0/freqY - 1.0)*expTerm + ((freqY - freqC)/freqY)*expTermK; //CC
       matrix[6]  = freqG*(1.0 - expTerm); //CG
       matrix[7]  = freqT + freqT*(1.0/freqY - 1.0)*expTerm - (freqT/freqY)*expTermK; //CT

       matrix[8]  = freqA + freqA*(1.0/freqR - 1.0)*expTerm - (freqA/freqR)*expTermK; //GA
       matrix[9]  = freqC*(1.0 - expTerm); //GC
       matrix[10] = freqG + freqG*(1.0/freqR - 1.0)*expTerm + ((freqR - freqG)/freqR)*expTermK; //GG
       matrix[11] = freqT*(1.0 - expTerm); //GT

       matrix[12] = freqA*(1.0 - expTerm); //TA
       matrix[13] = freqC + freqC*(1.0/freqY - 1.0)*expTerm - (freqC/freqY)*expTermK; //TC
       matrix[14] = freqG*(1.0 - expTerm); //TG
       matrix[15] = freqT + freqT*(1.0/freqY - 1.0)*expTerm + ((freqY - freqT)/freqY)*expTermK; //TT
}{% endhighlight %}			
			
		

### Adding a description and citation

We are almost ready to try out the new package, but BEAST 2 forces us to provide a brief description of the F84 class. To do this, add the following line just before <code>public class F84 extends SubstitutionModel.Base</code>:

{% highlight xml %}@Description("F84 substitution model"){% endhighlight %}			

(Ignore the light bulb icon for the moment.) We can also add citations, which are recommended to users of the package as papers they should cite. Add the following line just after the @Description:

{% highlight xml %}@Citation("Kishino, H., and M. Hasegawa. 1989. Evaluation of the maximum likelihood estimate of the evolutionary tree topologies from DNA sequence data, and the branching order in Hominoidea. Journal of Molecular Evolution 29:170-179."){% endhighlight %}			

There should be no line breaks in either the <code>@Description</code> or <code>@Citation</code> lines: if lines are broken, you should fix these manually so that each of these declarations occupies just one line (a long one in the case of <code>@Citation</code>).

You should now see two more light-bulb-red X icons, one beside the <code>@Description</code> line and another beside the <code>@Citation</code> line. Clicking these icons may result in a message like this: &#8220;Description cannot be resolved to a type&#8221; and no possible actions are suggested. If this happens (and we&#8217;re still not sure why it happens), hover your mouse over the words <code>Description</code>and <code>Citation</code>, in turn, and this should give you a &#8216;quick fix&#8217; option to <b>Import &#8216;Description&#8217; (beast.core)</b> and  <b>Import &#8216;Citation&#8217; (beast.core)</b>, respectively. After choosing these &#8216;quick fixes&#8217;, you should see this import declarations at the top of your file:

{% highlight xml %}import beast.base.core.Citation;
import beast.base.core.Description;{% endhighlight %}			

Of course, you can always just type in the declarations directly rather than using Eclipse&#8217; &#8216;quick fixes&#8217;

### Create a version.xml XML file

The <b>version.xml</b> file contains information about dependencies of your package and declares which services you package provides. First, we declare the package name and version

{% highlight xml %}<package name="MyPackage" version="0.0.1">
</package>{% endhighlight %}			

Then we declare the dependency on <b>BEAST.base</b> and its version number:

{% highlight xml %}
     <depends on='BEAST.base' atleast='2.7.0'/>
{% endhighlight %}

Finally, we declare the <b>BEASTInterface</b> service provided by our F84 substitution model. 

{% highlight xml %}    <service type="beast.base.core.BEASTInterface">
        <provider classname="mypackage.evolution.substitutionmodel.F84"/>
    </service>{% endhighlight %}

Since we provide only a single service, there will be just one entry, but if you have more services (e.g. package specific operators), you can have multiple entries. The complete <b>version.xml</b> file looks like this:

{% highlight xml %}<package name="MyPackage" version="0.0.1">
    <depends on='BEAST.base' atleast='2.7.0'/>

    <service type="beast.base.core.BEASTInterface">
        <provider classname="mypackage.evolution.substitutionmodel.F84"/>
    </service>
</package>{% endhighlight %}

Save it in the <b>$HOME/Documents/workspace/MyPackage</b> directory.

## Debugging the new package inside Eclipse

### Create an example XML file

Because the F84 model is so similar to the HKY model, we can copy the <i>textHKY.xml</i> file and replace the HKY-specific parts with F84 counterparts to obtain an XML file that can be used to test our F84 package. The following instructions show how to do this.

<h4>Create an examples folder</h4>

Right-click &#8220;MyPackage&#8221; in the Project Explorer pane of Eclipse to obtain a popup menu, then choose <b>New > Folder</b>. Name the new folder &#8220;examples&#8221;.

<h4>Copy and rename the testHKY.xml file</h4>

In the <b>Project Explorer</b> pane of Eclipse, press the disclosure triangle beside the &#8220;beast2&#8221; project to expand, then expand the &#8220;examples&#8221; folder within &#8220;beast2&#8221; and copy the <i>testHKY.xml</i>file. Now paste it into the new &#8220;examples&#8221; folder inside the &#8220;MyPackage&#8221; project. To rename the <i>testHKY.xml</i> file to <i>testF84.xml</i>, right-click it to bring up the popup menu, then select <b>Rename&#8230;</b>.

<h4>Replace HKY/kappa with F84/kF84 throughout testF84.xml</h4>

The <i>testF84.xml</i> file still has several references to the HKY model. These need to be changed to be references to the new F84 class. To edit the file in Eclipse, double-click its name in the <b>Project Explorer</b> pane. This will likely drop you into <b>Design</b> view; to switch to the more useful <b>Source</b> view, click on the <b>Source</b> tab at the bottom left corner of the editor pane. Use the <b>Edit > Find/Replace&#8230;</b> menu command to bring up the <b>Find/Replace</b> dialog and (case-sensitively) 

* replace spec='HKY' with spec='mypackage.evolution.substitutionmodel.F84'
* replace all instances of &#8220;HKY&#8221; with &#8220;F84&#8221; 
* replace all instances of &#8220;kappa&#8221; with &#8220;kF84&#8221;, then <b>File > Save</b> the file.

### Create a debug configuration in Eclipse

To create a new debug configuration, follow these steps.

o First, open the &#8220;BeastFX&#8221; project in the <b>Project Explorer</b> pane, then expand &#8220;src&#8221;, then expand &#8220;beastfx.app.beast&#8221; and, finally, select &#8220;BeastMain.java&#8221;

o Use <b>Run > Debug As&#8230; > Java Application</b>. This will bring up a <b>BEAST</b> dialog box, but by the time this dialog box appears, a new debug configuration has been created, so you can immediately press the <b>Quit</b> button to dismiss the dialog box.

o Now that a debug configuration exists, you must modify it to debug your new package rather than BEAST 2.

o Use <b>Run > Debug Configurations&#8230;</b> to bring up the <b>Debug Configurations</b> dialog

o Change the <b>Project</b> from &#8220;beast2&#8221; to &#8220;MyPackage&#8221; by clicking on the <b>Browse..</b>. button, then choosing &#8220;MyPackage&#8221;

o Click on the <b>Arguments</b> tab and type &#8220;-version_file version.xml examples/testF84.xml&#8221; in the <b>Program arguments</b> field to specify the version.xml file that declares the F84 class, and that the <i>testF84.xml</i> file should be run when debugging.

o While still on the <b>Arguments</b> tab, enter <code>-Djava.only=true</code> in the <b>VM arguments</b> field. This is really only necessary if you have the BeagleLib GPU library installed. Setting <code>java.only</code>to <code>true</code> means that BeagleLib will not be used even if it is available. Later, we will implement the <code>getEigenDecomposition</code> function in the <i>F84.java</i> file and, after that, BeagleLib can be used in analyses involving the new F84 model.

### Running in debug mode

To run in debug mode, go the the main Eclipse <b>Run</b> menu, choose <b>Debug configurations&#8230;</b>, select &#8220;BeastMCMC&#8221; under <b>Java Application</b>, then press the <b>Debug</b> button to start. After running this configuration once, you can simply choose <b>Run > Debug History > BeastMCMC</b> to start debugging (and you will find a convenient button on the main toolbar that provides a shortcut to a &#8220;BeastMCMC&#8221; debug session as well).

If the debug session starts successfully, you should see output similar to that below:

{% highlight xml %}        Sample treeLikelihood ESS(tre_ihood)       F84.kF84  ESS(F84.kF84)
             0     -4448.0995              N         2.0                 N --
         10000     -1818.1884         2.0            5.8056         2.0    --
         20000     -1818.3355         3.0            5.9649         3.0    --
         30000     -1816.2929         4.0            7.8015         4.0    --
         40000     -1819.5829         5.0            8.0218         3.4    --
         50000     -1815.1960         6.0           10.8317         2.9    --
         60000     -1813.7161         7.0           10.8668         2.8    15s/Msamples
         70000     -1814.7439         8.0           10.5266         2.7    14s/Msamples
         80000     -1818.4482         9.0            7.2835         3.2    14s/Msamples
         90000     -1819.8785         9.0            6.5495         4.0    14s/Msamples
        100000     -1821.1832         6.1            5.9992         3.9    14s/Msamples
        110000     -1816.3276         8.5            7.1380         4.1    14s/Msamples
        120000     -1816.2799        10.5            7.3529         4.3    14s/Msamples
        130000     -1817.5722        11.5            9.7990         5.4    14s/Msamples{% endhighlight %}		

## Adding the F84 model to BEAUti

It is convenient to incorporate new packages into BEAUti so that users of your package are not required to hand-craft an XML file. Incorporating an package into BEAUti requires adding a template to your package project. The template tells BEAUti what information to collect for your package.

### Create a template

First, create a new folder called <b>fxtemplates</b> under &#8220;MyPackage&#8221; inside the <b>Project Explorer</b> pane in Eclipse. The easiest way to do this is to right-click (or Control-click) the &#8220;MyPackage&#8221; project, then choose <b>New > Folder</b> from the resulting popup menu.

Now create a text file within the new fxtemplates folder named <i>F84-beauti-template.xml</i>. The easiest way to do this is to right-click (or Control-click) the fxtemplates folder within the &#8220;MyPackage&#8221; project, then choose <b>New > File</b> from the resulting popup menu, giving the new file the name &#8220;F84-beauti-template.xml&#8221;. Copy the text below into the new <i>F84-beauti-template.xml</i> file and save it.

{% highlight xml %}<beast version='2.0' 
	namespace='beastfx.app.beauti:beastfx.app.inputeditor:beast.pkgmgmt:beast.base.core:beast.base.inference:beast.base.evolution.branchratemodel:beast.base.evolution.speciation:beast.base.evolution.tree.coalescent:beast.base.util:beast.base.math:beast.evolution.nuc:beast.base.evolution.operator:beast.base.inference.operator:beast.base.evolution.sitemodel:beast.base.evolution.substitutionmodel:beast.base.evolution.likelihood:beast.evolution:beast.base.inference.distribution'>

	<mergewith point='substModelTemplates'>
		
		<subtemplate id='F84' class='mypackage.evolution.substitutionmodel.F84' mainid='F84.s:$(n)'>
		<![CDATA[
			<plugin spec='mypackage.evolution.substitutionmodel.F84' id='F84.s:$(n)'>
				<parameter id="kF84.s:$(n)" name='kF84' value="2.0" lower="0.0" estimate='true'/>
				<frequencies id='estimatedFreqs.s:$(n)' spec='Frequencies'>
					<frequencies id='freqParameter.s:$(n)' spec='parameter.RealParameter' dimension='4' value='0.25' lower='0' upper='1'/>
				</frequencies>
			</plugin>
			
			<operator id='kF84Scaler.s:$(n)' spec='kernel.BactrianScaleOperator' scaleFactor="0.1" weight="1" parameter="@kF84.s:$(n)"/>
			<operator id='FrequenciesExchanger.s:$(n)' spec='kernel.BactrianDeltaExchangeOperator' delta="0.01" weight="0.1" parameter="@freqParameter.s:$(n)"/>
				
			<prior id='kF84Prior.s:$(n)' x='@kF84.s:$(n)'>
				<distr spec="LogNormalDistributionModel" meanInRealSpace='true'>
					<parameter name='M' value="1.0" estimate='false'/>
					<parameter name='S' value="1.25" estimate='false'/>
				</distr>
			</prior>
		]]>
		
		<connect srcID='kF84.s:$(n)' targetID='state' inputName='stateNode' if='inposterior(F84.s:$(n)) and kF84.s:$(n)/estimate=true'/>
		<connect srcID='freqParameter.s:$(n)' targetID='state' inputName='stateNode' if='inposterior(F84.s:$(n)) and inposterior(freqParameter.s:$(n)) and freqParameter.s:$(n)/estimate=true'/>
		<connect srcID='kF84Scaler.s:$(n)' targetID='mcmc' inputName='operator' if='inposterior(F84.s:$(n)) and kF84.s:$(n)/estimate=true'>Scale F84 transition-transversion parameter of partition $(n)</connect>
		<connect srcID='FrequenciesExchanger.s:$(n)' targetID='mcmc' inputName='operator' if='inposterior(F84.s:$(n)) and inposterior(freqParameter.s:$(n)) and freqParameter.s:$(n)/estimate=true'>Exchange values of frequencies of partition $(n)</connect>
		<connect srcID='kF84.s:$(n)' targetID='tracelog' inputName='log' if='inposterior(F84.s:$(n)) and kF84.s:$(n)/estimate=true'/>
		<connect srcID='freqParameter.s:$(n)' targetID='tracelog' inputName='log' if='inposterior(F84.s:$(n)) and inposterior(freqParameter.s:$(n)) and freqParameter.s:$(n)/estimate=true'/>
		<connect srcID='kF84Prior.s:$(n)' targetID='prior' inputName='distribution' if='inposterior(F84.s:$(n)) and kF84.s:$(n)/estimate=true'>F84 transition-transversion parameter of partition $(n)</connect>
		
		</subtemplate>
	</mergewith>
</beast>{% endhighlight %}			

### Run BEAUti

To create a debug configuration that runs BEAUti, choose <b>Run > Debug Configurations&#8230;</b> from the main Eclipse menu. In the <b>Debug Configurations</b> dialog, select <b>Java Application</b> and click the <b>New launch configuration</b> button. In the <b>Name</b> field at the top of the dialog box, type &#8220;BEAUti&#8221;. In the <b>Project</b> field of the <b>Main</b> tab, type (or use the Browse&#8230; button to select) &#8220;MyPackage&#8221;. For the <b>Main class</b>, type &#8220;beastfx.app.beauti.Beauti&#8221;. In the Arguments tab, add &#8220;-version_file ../MyPackage/version.xml&#8221; to the program arguments, and set Working directory to &#8220;other&#8221; and fill in &#8220;../BeastFX&#8221;.


NB Temporarily copy the file `MyPackage/fxtemplates/F84.xml` into `BeastFX/fxtemplates` so BEAUti picks it up from there.


Now you should be able to click the Debug button to run BEAUti. Try using the following steps to generate a BEAST 2 XML file:

o Click the + button at the lower left, then navigate to the <i>beast2/examples/nexus</i> folder and select the <i>anolis.nex</i> file. (You can avoid doing this step if you specify &#8220;-nex ../beast2/examples/nexus/anolis.nex&#8221; in the &#8220;Program arguments&#8221; field of the Arguments tab in the BEAUti debug configuration.)

o Click the <b>Site Model</b> tab and select the new F84 model in the <b>Subst Model</b> drop down list

o Click the <b>MCMC</b> tab, and set <b>Chain Length</b> to 1000000 (the default is 10 million, which will take longer than you probably want to wait for a test example)

o Expand the <b>tracelog</b> logger and make sure <b>Log Every</b> is set to 1000

o Use <b>File > Save As&#8230;</b> to save the XML file under the name <i>f84example.xml</i> in the <i>MyPackage/examples</i> folder

NB Don't forget to delete `BeastFX/fxtemplates/F84.xml`


### Run the <i>f84example.xml</i> file

The goal of this section is to create a debug configuration that is essentially a copy of the first one we created (in the section entitled &#8220;Create a debug configuration in Eclipse&#8221;) with the exception that the file that is run is <i>f84example.xml</i> instead of <i>testF84.xml</i>. The easiest way to create the new configuration is to simply duplicate the existing one. Here are instructions for accomplishing that:

o Use the menu command <b>Run > Debug Configurations&#8230;</b> to open the <b>Debug Configurations</b> dialog box

o Under <b>Java Application</b>, select the &#8220;BeastMain&#8221; configuration

o Right-click (Control-click on a Mac) and choose <b>Duplicate</b> from the popup menu

o Select the new configuration, which for us was automatically named &#8220;BeastMain (1)&#8221;

o Change the <b>Project:</b> field to <b>MyPackage</b>.

o Click the <b>Arguments</b> tab and change the <b>Program arguments</b> field to <i>-version_file version.xml examples/f84example.xml</i>

o If necessary, move the <i>f84example.xml</i> file inside the <i>MyPackage/examples</i> directory so that its location matches the specification you just provided in the Program arguments field

You should now be able to click the <b>Debug</b> button to start an analysis of the <i>f84example.xml</i> file.

The maximum likelihood estimates on a neighbor-joining tree evaluated using PAUP* were:

{% highlight xml %}Tree                    1
-------------------------
-ln L         22466.61861
Base frequencies:
  A              0.277138
  C              0.273218
  G              0.107937
  T              0.341707
Ti/tv:
  k              0.970507{% endhighlight %}			

The corresponding posterior means we obtained from running the F84 package in BEAST 2 were as follows:

{% highlight xml %}Base frequencies:
  A              0.276
  C              0.274
  G              0.108
  T              0.343
Ti/tv:
  k              0.928{% endhighlight %}			

The Bayesian estimate is lower than the maximum likelihood estimate due to the fact that we used the default prior on <i>k</i>, which is LogNormal with mean and standard deviation (on the log scale) of 1 and 1.25, respectively. Half the mass of this prior lies below 0.46, which pulls the posterior mean down slightly.

## Preparing your package for release

In order to publish your package, you will need to compile it outside of Eclipse and build a jar file that you can distribute. Here is an overview of the steps involved:

o compile your package using ant to create a zip file

o get your package published on the BEAST 2 web site so that others can install it over the internet

### Compile beast2 using ant

Navigate (in a Terminal window) to the <i>beast2</i> project directory. If you have been following this tutorial faithfully, the folder should be <i>$HOME/Documents/workspace/beast2</i>. Type <i>ant</i> at the command line. (If <i>ant</i> cannot be found, you may need to install it from the Apache Ant website.) This will build BEAST 2 using the <i>build.xml</i> file supplied with BEAST 2. It is necessary to build BEAST 2 again (even though we&#8217;ve already built it in Eclipse) because Eclipse places its object files and intermediate build products in a different directory than does ant.

### Complete MyPackage files

Add a README.md file in the <i>$HOME/Documents/workspace/MyPackage</i> directory containing a description of the package and (links to) instructions on how to use it.

```
This is my [BEAST 2](http://beast2.org) package containing the F84 substitution model.

It can be accessed in BEAUti in the Site Model panel.
```

Add a license in the COPYING file in <i>$HOME/Documents/workspace/MyPackage</i>. If you want to use the LGPL, it can be copied from the beast2 directory.


### Compile MyPackage using ant

The next step is to create a <i>build.xml</i> file within the <i>$HOME/Documents/workspace/MyPackage</i> folder that tells ant how to build MyPackage. Create a new text file (using Eclipse or any way you like) just inside the <i>MyPackage</i> directory. Name the new file <i>build.xml</i> and enter the text below into the new file and save it.

{% highlight xml %}
<project default="build" basedir=".">

    <!-- Source, JUnit test code and jar library locations. -->
    <property name="src" location="src"/>
    <property name="test" location="test"/>
    <property name="lib" location="lib"/>

    <!-- Location to check for local copy of beast2 repository -->
    <property name="local-beast-source-root" location="../beast2"/>

    <!-- BEAST 2 currently uses Java 1.8 -->
    <property name="sourceVersion" value="1.8"/>
    <property name="targetVersion" value="1.8"/>

    <!-- Directories necessary for all BEAST 2 packages -->
    <property name="doc" location="doc"/>
    <property name="examples" location="examples"/>
    <property name="scripts" location="scripts"/>
    <property name="fxtemplates" location="fxtemplates"/>

    <!-- BEAST branch and version to build against
         (only different for version tags because of
         a Github peculiarity) -->
    <property name="beast-branch" value="master"/>
    <property name="beast-version" value="master"/>

    <!-- Names of temporary build/test directories -->
    <property name="build" location="build"/>
    <property name="build-beast" location="build-beast"/>
    <property name="beast-source-root" location="beast-source"/>
    <property name="build-test" location="build-test"/>
    <property name="test-reports" location="test-reports"/>
    <property name="dist" location="dist"/>
    <property name="pack" location="${dist}/package"/>

    <!-- Prepare for compilation -->
    <target name="init">

        <available file="version.xml" property="versionAvailable"/>
        <fail unless="versionAvailable">
            ** Required file version.xml does not exist. **
            If this is a new project, run "ant skeleton" from
            the command line to create the files required for
            your BEAST 2 package.
        </fail>

        <!-- Read package name and version from xml file -->
        <xmlproperty file="version.xml" prefix="fromVersionFile" />
        <property name="projName" value="${fromVersionFile.package(name)}" />
        <property name="projVersion" value="${fromVersionFile.package(version)}" />

        <mkdir dir="${build}"/>
        <mkdir dir="${build-beast}"/>
        <mkdir dir="${beast-source-root}"/>
        <mkdir dir="${dist}"/>
    </target>

    <!-- Get beast -->

    <target name="find-beast" depends="init">
        <available file="${local-beast-source-root}" property="localBeastAvailable"/>
    </target>

    <target name="get-remote-beast" depends="find-beast" unless="localBeastAvailable">
        <echo>No local copy of the beast2 source found at ${local-beast-source-root}.</echo>
        <echo>Compiling against version ${beast-version} from GitHub.</echo>

        <mkdir dir="beast-archive"/>

        <get src="https://github.com/CompEvol/beast2/archive/${beast-branch}.zip"
             dest="beast-archive/beast.zip"/>
        <unzip src="beast-archive/beast.zip" dest="beast-archive"/>

        <copy todir="${beast-source-root}">
              <fileset dir="beast-archive/beast2-${beast-version}" includes="**/*.*"/>
        </copy>

        <delete dir="beast-archive"/>
    </target>

    <target name="get-local-beast" depends="find-beast" if="localBeastAvailable">
        <echo>Compiling against beast2 source found at ${local-beast-source-root}.</echo>

        <copy todir="${beast-source-root}">
          <fileset dir="${local-beast-source-root}" includes="**/*.*"/>
        </copy>
    </target>

    <target name="build-beast" depends="get-local-beast,get-remote-beast">
        <javac 
            srcdir="${beast-source-root}/src"
            destdir="${build-beast}"
            source="${sourceVersion}"
            target="${targetVersion}"
            includeantruntime="false"
            fork="yes">
            <classpath>
                <pathelement path="${classpath}"/>
                <fileset dir="${beast-source-root}/lib" includes="**/*.jar"/>
            </classpath>
        </javac>
    </target>

    <!-- Compile -->
    <target name="compile" depends="build-beast">
      <javac target="${targetVersion}" source="${sourceVersion}"
             srcdir="${src}" destdir="${build}"
             includeantruntime="false" fork="yes">
            <classpath>
                <pathelement path="${classpath}"/>
                <fileset dir="${lib}" includes="**/*.jar"/>
                <fileset dir="${beast-source-root}/lib" includes="**/*.jar"/>
                <pathelement path="${build-beast}"/>
            </classpath>
        </javac>
    </target>

    <target name="copy-resources" depends="compile">
        <copy todir="${build}">
            <fileset dir="${src}"
                     includes="**/*.png" />
        </copy>
    </target>

    <!-- Prepare for unit test compilation -->
    <target name="init-test" depends="init">
        <mkdir dir="${build-test}"/>
        <mkdir dir="${test-reports}"/>
    </target>

    <!-- Compile unit tests -->
    <target name="compile-test" depends="init-test,compile,copy-resources">
      <javac target="${targetVersion}" source="${sourceVersion}"
             srcdir="${test}" destdir="${build-test}"
             includeantruntime="false" fork="yes">
            <classpath>
                <pathelement path="${classpath}"/>
                <pathelement path="${build}" />
                <fileset dir="${lib}" includes="**/*.jar"/>
                <fileset dir="${beast-source-root}/lib" includes="**/*.jar"/>
                <pathelement path="${build-beast}"/>
            </classpath>
        </javac>
    </target>


    <!-- Run unit tests -->
    <target name="test" depends="compile-test">
        <junit printsummary="yes" failureproperty="testFailed" showoutput="true">
            <classpath>
                <pathelement path="${classpath}"/>
                <pathelement path="${build}" />
                <pathelement path="${build-test}" />
                <fileset dir="${beast-source-root}/lib" includes="**/*.jar"/>
                <pathelement path="${build-beast}" />
            </classpath>
            <batchtest fork="yes" todir="${test-reports}">
                <fileset dir="${test}">
                    <include name="**/*.java"/>
                </fileset>
                <formatter type="plain"/>
                <formatter type="plain" usefile="false"/> <!-- to screen -->
            </batchtest>
        </junit>

        <fail if="testFailed" status="1" message="Unit test failed."/>
    </target>


    <!-- Create BEAST 2 package -->
    <target name="build" depends="compile,copy-resources">
        <property name="fullName" value="${projName}.v${projVersion}"/>

        <mkdir dir="${pack}"/>
        <mkdir dir="${pack}/examples"/>
        <mkdir dir="${pack}/scripts"/>
        <mkdir dir="${pack}/fxtemplates"/>
        <mkdir dir="${pack}/lib"/>
        <mkdir dir="${pack}/doc"/>

        <jar jarfile="${pack}/${fullName}.src.jar" basedir="${src}" />

        <mkdir dir="${lib}" />
        <copy todir="${pack}/lib">
            <fileset dir="${lib}" includes="*.jar" />
        </copy>
        <jar jarfile="${pack}/lib/${fullName}.jar" basedir="${build}" />

        <copy file="README.md" tofile="${pack}/README" />
        <copy file="COPYING" todir="${pack}" />
        <copy todir="${pack}">
            <fileset dir="${lib}" includes="LICENSE*" />
        </copy>

        <mkdir dir="${examples}" />
        <copy todir="${pack}/examples">
            <fileset dir="${examples}" includes="**/*.xml" />
        </copy>

        <mkdir dir="${scripts}" />
        <copy todir="${pack}/scripts">
            <fileset dir="${scripts}" includes="**/*" />
        </copy>


        <mkdir dir="${fxtemplates}" />
        <copy todir="${pack}/fxtemplates">
            <fileset dir="${fxtemplates}" includes="*.xml" />
        </copy>

        <mkdir dir="${doc}" />
        <copy todir="${pack}/doc">
            <fileset dir="${doc}" includes="*.tex,*.doc,*.lyx,*.txt"/>
        </copy>

        <copy file="version.xml" todir="${pack}" />

        <zip destfile="${dist}/${fullName}.zip" basedir="${pack}" />

        <delete dir="${pack}"/>

        <echo/>
        <echo/>
        <echo>** Package ${dist}/${fullName}.zip created successfuly! **</echo>
    </target>


    <!-- Revert to pristine state. -->
    <target name="clean">
        <delete dir="${build}" />
        <delete dir="${build-beast}" />
        <delete dir="${beast-source-root}" />
        <delete dir="${dist}" />
        <delete dir="${build-test}" />
        <delete dir="${test-reports}" />
    </target>

 	<condition property="isWindows"><os family="windows" /></condition>
	<condition property="isLinux"><and><os family="unix"/><not><os family="mac"/></not></and></condition>
	<condition property="isMac"><os family="mac" /></condition>

	<target name="installLinux" depends="build" if="isLinux">
	        <mkdir dir="${user.home}/.beast/2.7/${projName}"/>
			<unzip src="${dist}/${fullName}.zip" dest="${user.home}/.beast/2.7/${projName}"/>
	</target>

	<target name="installMac" depends="build" if="isMac">
	        <mkdir dir="${user.home}/.beast/2.7/${projName}"/>
			<unzip src="${dist}/${fullName}.zip" dest="${user.home}/Library/Application Support/BEAST/2.7/${projName}"/>
	</target>

	<target name="installWindows" depends="build" if="isWindows">
	        <mkdir dir="${user.home}/BEAST/2.7/${projName}"/>
			<unzip src="${dist}/${fullName}.zip" dest="${user.home}/BEAST/2.7/${projName}"/>
	</target>

	<target name="install" depends="installWindows,installMac,installLinux">
	</target>
	
</project>
 {% endhighlight %}			
			
You can now compile your package by changing directory to the <i>MyPackage</i> folder and typing

{% highlight xml %}ant{% endhighlight %}			

### Move the package to a place where BEAUti can find it

Once ant has finished compiling the package, you should have a <i>MyPackage/dist/MyPackage.v0.0.1.zip</i> file inside your Eclipse workspace directory. For testing purposes, this file should be copied to <i>$HOME/.beast2/2.7/MyPackage</i> and unzipped. If you have not yet run BEAUti outside of Eclipse, you may not yet have a <i>$HOME/.beast2/2.7</i> directory. Also, you will need to create the <i>MyPackage</i> subdirectory inside the <i>$HOME/.beast2/2.7</i> directory.

{% highlight xml %}mkdir -p ~/.beast2/2.7/MyPackage
cp ~/workspace/beast2/dist/MyPackage.v0.0.1.zip ~/.beast2/2.7/MyPackage
cd ~/.beast2/2.7/MyPackage
unzip MyPackage.v0.0.1.zip
rm MyPackage.v0.0.1.zip
{% endhighlight %}			

This should create the following directory structure:

{% highlight xml %}$HOME
    .beast
        2.7
            F84
                META-INF
                    MANIFEST.MF
                lib
                    F84.package.jar
                fxtemplates
                examples
{% endhighlight %}			

To install the package via ant instead of by hand:

{% highlight xml %}ant install{% endhighlight %}			

## Files included in this project

The complete set of files, including java code, BEAUti template, ant script, etc. is available as <a href="https://www.beast2.org/package-development-guide/MyPackage.zip">MyPackage.zip</a>.
