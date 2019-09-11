---
layout: site
title: Writing a BEAST 2 Package 
tags: []
---

# Writing a BEAST 2 Package

The goal of this tutorial is to illustrate how to create an package for BEAST 2. You will create an package that adds a new nucleotide substitution (F84) model to BEAST 2. The tutorial assumes that the reader is using MacOS 10.8.5, but the tutorial should be reasonably portable. This tutorial was created by the “Add-On Tutorial and SDK” breakout group at the third NESCent BEAST meeting, Dec. 5-8, 2011, and revised at the fourth NESCent BEAST meeting, Dec. 3-6, 2013.  Please contact [Paul Lewis](mailto:paul.lewis@uconn.edu), [Peter Beerli](mailto:beerli@fsu.edu), or [Remco Bouckaert](mailto:r.bouckaert@auckland.ac.nz) with comments/suggestions. **Last updated:** Sep. 2019.

## Installing Eclipse

We will use the Eclipse IDE to manage the new package project, so the first step is to install Eclipse if you do not already have it on your system. Go to [http://www.eclipse.org/downloads/](http://www.eclipse.org/downloads/) and download “Eclipse IDE for Java Developers” for your system. This tutorial was written while using version 2019-03 (4.11.0) of Eclipse. For our MacOS 10.8.5 system, we unzipped the downloaded Eclipse `tar.gz` file by double-clicking it, then moved the unpacked folder (named _eclipse_) to our Applications folder. You may wish to drag _Eclipse.app_ to your dock to make it easy to open. 

On opening Eclipse for the first time, you will be asked to select a workspace. For the purpose of this tutorial, we will assume you chose this directory:

```xml
$HOME/Documents/workspace
```

## Downloading the BEAST 2 source

Now, open Terminal app (in the MacOS Utilities folder) and navigate to the Eclipse workspace directory by typing this command:

```xml
cd $HOME/Documents/workspace
```

Assuming Subversion is installed, you should be able to download the BEAST 2 source code using this command:

```xml
git clone https://github.com/CompEvol/beast2.git
```

if you use http, or

```xml
git clone git@github.com:CompEvol/beast2.git
```
if you use ssh.

## Creating a BEAST 2 project
 
Back in Eclipse, use **File > New > Project… > Java project** to create a new Java project named “beast2”. Press **Next** and then untick the **Create module.info.java file** checkbox if it appears. Go to the libraries tab at the top, click on **DensiTree.jar** and then press **Remove**.  Once you press the **Finish** button, Eclipse will proceed to compile “beast2”. If a dialog appears prompting you to create module.info.java, select _Don't create_.

## Creating a project for “MyPackage”

Now you need to create an Eclipse project for your package and make it depend on the “beast2” project.

- Use **File > New > Java project** to create a new Java project named “MyPackage”. Press **Next** and then untick the **Create module.info.java file** checkbox if it appears. Press the **Finish** button. Now you should see two projects in the **Project Explorer** pane: “beast2” and “MyPackage”. If you see **Package Explorer** rather than **Project Explorer**, use **Window > Show View > Project Explorer** to bring up the **Project Explorer** view instead.

- Right-click (or Control-click if you do not have a mouse with two buttons) “MyPackage” and choose **Properties** from the speed menu that appears

- Choose **Java Build Path**, click the **Projects** tab, select **Class path**, click the **Add…** button, then check “beast2” and press the **OK** button. Now press the **OK** button in the **Properties for MyPackage** dialog box to close it.

## Creating a package for “MyPackage”

With “MyPackage” selected in the Project Explorer, use **File > New > Package** to create a package. The **Source folder** for the package should be automatically filled in correctly (“MyPackage/src”), and the **name** should be set to “beast.evolution.substitutionmodel”. If Eclipse does not allow you to press the **Finish** button, cancel the dialog box, locate “beast.evolution.substitutionmodel” in the “beast2” project, copy it, then try again to create the package, this time pasting in “beast.evolution.substitutionmodel”.

## Creating an F84 class

Now right-click (or Control-click) on the “beast.evolution.substitutionmodel” package under “MyPackage” and choose **New > Class** from the resulting popup menu. Give the new class the name “F84” and press **Finish**. You should now see the _F84.java_ file open in the editor, and it should contain the following text: package beast.evolution.substitutionmodel;

```xml
public class F84 {

}
```

Add “extends SubstitutionModel.Base” following the class name to produce the following:

```xml
package beast.evolution.substitutionmodel;

public class F84 extends SubstitutionModel.Base {

}
```

At this point you should see an icon beside the class declaration. The icon consists of a light bulb with a red square containing a white letter X. Clicking this should bring up a popup menu that allows you to **Add unimplemented methods**. Your _F84.java_ file should now look like this:

```xml
package beast.evolution.substitutionmodel;

import beast.evolution.datatype.DataType;
import beast.evolution.tree.Node;

public class F84 extends SubstitutionModel.Base {

	@Override
	public void getTransitionProbabilities(Node node, double startTime, double endTime, double rate, double[] matrix) {
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
}
```

Note: depending on your particular version of Eclipse (or some other as-yet-unidentified factor), the methods that appear may not be in the order listed above. These are methods that must be implemented because they are abstract methods in the `SubstitutionModel.Base` base class.

### Adding the k parameter

The F84 model is comprised of nucleotide frequency parameters as well as a parameter _k_ that determines the number of extra transition-type substitutions (above and beyond those generated by the general substitution process). If _k_ equals zero, then the F84 model collapses to the F81 model, which allows nucleotide composition to vary but does not allow transition/transversion bias. As _k_ becomes larger, transitions become more common. The nucleotide frequencies are supplied by the base class, but the _k_ parameter is unique to the F84 model and thus must be defined by our package class. To do this, add the following line to the class definition just after public class:

```xml
F84 extends SubstitutionModel.Base {
   public Input<RealParameter> kF84 = new Input<RealParameter>("kF84", "k parameter in the F84 model", Validate.REQUIRED);
   .
   .
   .
}
```

Adding the `kF84` parameter will require you to insert some declarations at the top of the file. Eclipse helps you do this:

- First, save the file

- You should see red lines under **Input**, **RealParameter**, and **Validate**

- Hover over **Input** and select **Import ‘Input’ (beast.core)** from the dialog menu

- Repeat until you have all three of these declarations inserted:

```xml
import beast.core.Input;
import beast.core.Input.Validate;
import beast.core.parameter.RealParameter;
```

### Adding the initAndValidate method

We must also override the initAndValidate method, within which a call to the super method allows initialization of the base class.

```xml
   @Override
   public void initAndValidate() {
   	super.initAndValidate();
   	kF84.get().setBounds(0.0, Double.POSITIVE_INFINITY);
   }
```

### Implementing the canHandleDataType method

The F84 model is a substitution model applicable only to nucleotide sequences, so replace the body of the automatically-created stub with the following:

```xml
@Override
public boolean canHandleDataType(DataType dataType) {
   if (dataType instanceof Nucleotide) {
     return true;
   }
   return false;
}
```

The method now returns true if dataType is an instance of the Nucleotide class, and otherwise throws an exception with an informative message. Adding this code requires the following declaration at the top of the file:

```xml
import beast.evolution.datatype.Nucleotide;
```

### Implementing the getEigenDecomposition method

For now, leave this method unimplemented. We will return to it later after testing the basic implementation.

### Implementing the getTransitionProbabilities method

The getTransitionProbabilities method is expected to fill a 16 element vector with transition probabilities in the order AA, AC, AG, AT, CA, CC, CG, CT, GA, GC, GG, GT, TA, TC, TG, TT, where the first nucleotide in each pair is the “from” state and the second in each pair is the “to” state. Each transition probability depends on the edge length, which is measured in terms of the expected number of subsitutions. The expected number of substitutions is the product of rate (provided by `rate`) and time (the difference between `endTime` and `startTime`). Note that time is viewed from the present looking toward the past, so `startTime - endTime` is the correct way to get the time difference. Fill in the `getTransitionProbabilities`method as follows:

```xml
@Override
public void getTransitionProbabilities(Node node, double startTime, double endTime, double rate, double[] matrix) {
       double[] freqs = frequencies.getFreqs();
       double freqA = freqs[0];
       double freqC = freqs[1];
       double freqG = freqs[2];
       double freqT = freqs[3];
       double freqR = freqA + freqG;
       double freqY = freqC + freqT;

       double k = kF84.get().getValue();
       double sumPiSquared = freqA*freqA + freqC*freqC + freqG*freqG + freqT*freqT;
       double sumPiRatios = freqA*freqA/freqR + freqC*freqC/freqY + freqG*freqG/freqR + freqT*freqT/freqY;
       double mu = (1.0 - sumPiSquared) + k*(1.0 - sumPiRatios);

       double distance = (startTime - endTime) * rate;
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
}
```

### Adding a description and citation

We are almost ready to try out the new package, but BEAST 2 forces us to provide a brief description of the F84 class. To do this, add the following line just before `public class F84 extends SubstitutionModel.Base`:

```xml
@Description("F84 substitution model")
```

We can also add citations, which are recommended to users of the package as papers they should cite. Add the following line just after the `@Description`:

```xml
@Citation("Kishino, H., and M. Hasegawa. 1989. Evaluation of the maximum likelihood estimate of the evolutionary tree topologies from DNA sequence data, and the branching order in Hominoidea. Journal of Molecular Evolution 29:170-179.")
```

There should be no line breaks in either the `@Description` or `@Citation` lines: if lines are broken, you should fix these manually so that each of these declarations occupies just one line (a long one in the case of `@Citation`).

Hover your mouse over the words `Description`and `Citation` and select **Import ‘Description’ (beast.core)** and **Import ‘Citation’ (beast.core)**, respectively. The following import declarations should appear at the top of your file:

```xml
import beast.core.Citation;
import beast.core.Description;
```

Of course, you can always just type in the declarations directly rather than using Eclipse’ ‘quick fixes’

## Debugging the new package inside Eclipse

### Create an example XML file

Because the F84 model is so similar to the HKY model, we can copy the _textHKY.xml_ file and replace the HKY-specific parts with F84 counterparts to obtain an XML file that can be used to test our F84 package. The following instructions show how to do this.

#### Create an examples folder

Right-click “MyPackage” in the Project Explorer pane of Eclipse to obtain a popup menu, then choose **New > Folder**. Name the new folder “examples”.

#### Copy and rename the testHKY.xml file

In the **Project Explorer** pane of Eclipse, press the disclosure triangle beside the “beast2” project to expand, then expand the “examples” folder within “beast2” and copy the _testHKY.xml_ file. Now paste it into the new “examples” folder inside the “MyPackage” project. To rename the _testHKY.xml_ file to _testF84.xml_, right-click it to bring up the popup menu, then select **Rename…**.

#### Replace HKY/kappa with F84/kF84 throughout testF84.xml

The _testF84.xml_ file still has several references to the HKY model. These need to be changed to be references to the new F84 class. To edit the file in Eclipse, double-click its name in the **Project Explorer** pane. This will likely drop you into **Design** view; to switch to the more useful **Source** view, click on the **Source** tab at the bottom left corner of the editor pane. Use the **Edit > Find/Replace…** menu command to bring up the **Find/Replace** dialog and (case-sensitively) replace all instances of “HKY with “F84” and all instances of “kappa” with “kF84”, then **File > Save** the file.

### Create a debug configuration in Eclipse

To create a new debug configuration, follow these steps.

- First, open the “beast2” project in the **Project Explorer** pane, then expand “src”, then expand “beast.app” and, finally, select “BeastMCMC.java”. 

- This should bring up a **BEAST** dialog box, but by the time this dialog box appears, a new debug configuration has been created, so you can immediately press the **Quit** button to dismiss the dialog box. If you receive an error message "Editor does not contain a main type", then press **Project > Properties...**, select the **Java Compiler** tab, check **Enable project specific settings**, set the **Compiler Compliance Level** to version 1.8, and then press **Apply and Close**.

- Now that a debug configuration exists, you must modify it to debug your new package rather than BEAST 2.

- Use **Run > Debug Configurations…** to bring up the **Debug Configurations** dialog

- Change the **Project** from “beast2” to “MyPackage” by clicking on the **Browse..**. button, then choosing “MyPackage”

- Click on the **Arguments** tab and type “examples/testF84.xml” in the **Program arguments** field to specify that the _testF84.xml_ file should be run when debugging

- While still on the **Arguments** tab, enter `-Djava.only=true` in the **VM arguments** field. This is really only necessary if you have the BeagleLib GPU library installed. Setting `java.only`to `true` means that BeagleLib will not be used even if it is available. Later, we will implement the `getEigenDecomposition` function in the _F84.java_ file and, after that, BeagleLib can be used in analyses involving the new F84 model.

- Press **Apply**.

### Running in debug mode

To run in debug mode, go the the main Eclipse **Run** menu, choose **Debug configurations…**, select “BeastMCMC” under **Java Application**, then press the **Debug** button to start. After running this configuration once, you can simply choose **Run > Debug History > BeastMCMC** to start debugging (and you will find a convenient button on the main toolbar that provides a shortcut to a “BeastMCMC” debug session as well).

When the BEAST window opens, press **Choose File** and navigate to _testF84.xml_. If the debug session starts successfully, you should see output similar to that below:

```xml
        Sample treeLikelihood ESS(tre_ihood)       F84.kF84  ESS(F84.kF84)
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
        130000     -1817.5722        11.5            9.7990         5.4    14s/Msamples
```

## Adding the F84 model to BEAUti

It is convenient to incorporate new packages into BEAUti so that users of your package are not required to hand-craft an XML file. Incorporating an package into BEAUti requires adding a template to your package project. The template tells BEAUti what information to collect for your package.

### Create a template

First, create a new folder called templates under “MyPackage” inside the **Project Explorer** pane in Eclipse. The easiest way to do this is to right-click (or Control-click) the “MyPackage” project, then choose **New > Folder** from the resulting popup menu.

Now create a text file within the new templates folder named _F84-beauti-template.xml_. The easiest way to do this is to right-click (or Control-click) the templates folder within the “MyPackage” project, then choose **New > File** from the resulting popup menu, giving the new file the name “F84-beauti-template.xml”. Copy the text below into the new _F84-beauti-template.xml_ file and save it.

```xml
<beast version='2.0' 
	namespace='beast.app.beauti:beast.core:beast.evolution.branchratemodel:beast.evolution.speciation:beast.evolution.tree.coalescent:beast.core.util:beast.evolution.nuc:beast.evolution.operators:beast.evolution.sitemodel:beast.evolution.substitutionmodel:beast.evolution.likelihood:beast.evolution:beast.math.distributions'>

	<mergewith point='substModelTemplates'>
		
		<subtemplate id='F84' class='beast.evolution.substitutionmodel.F84' mainid='F84.s:$(n)'>
		<![CDATA[
			<plugin spec='F84' id='F84.s:$(n)'>
				<parameter id="kF84.s:$(n)" name='kF84' value="2.0" lower="0.0" estimate='true'/>
				<frequencies id='estimatedFreqs.s:$(n)' spec='Frequencies'>
					<frequencies id='freqParameter.s:$(n)' spec='parameter.RealParameter' dimension='4' value='0.25' lower='0' upper='1'/>
				</frequencies>
			</plugin>
			
			<operator id='kF84Scaler.s:$(n)' spec='ScaleOperator' scaleFactor="0.5" weight="1" parameter="@kF84.s:$(n)"/>
			<operator id='FrequenciesExchanger.s:$(n)' spec='DeltaExchangeOperator' delta="0.01" weight="0.1" parameter="@freqParameter.s:$(n)"/>
				
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
</beast>
```


### Run BEAUti

To create a debug configuration that runs BEAUti, choose **Run > Debug Configurations…** from the main Eclipse menu. In the **Debug Configurations** dialog, select **Java Application** and click the **New launch configuration** icon (a white sheet of paper with a yellow plus in its top right corner). In the **Name** field at the top of the dialog box, type “BEAUti”. In the **Project** field of the **Main** tab, type (or use the Browse… button to select) “MyPackage”. For the **Main class**, type “beast.app.beauti.Beauti”. Press **Apply**.

Now you should be able to click the Debug button to run BEAUti. Try using the following steps to generate a BEAST 2 XML file:

- Click the + button at the lower left of the BEAUTi window, then navigate to the _beast2/examples/nexus_ folder and select the _anolis.nex_ file. (You can avoid doing this step if you specify “-nex ../beast2/examples/nexus/anolis.nex” in the “Program arguments” field of the Arguments tab in the BEAUti debug configuration.)

- Click the **Site Model** tab and select the new F84 model in the **Subst Model** drop down list

- Click the **MCMC** tab, and set **Chain Length** to 1000000 (the default is 10 million, which will take longer than you probably want to wait for a test example)

- Expand the **tracelog** logger and make sure **Log Every** is set to 1000

- Use **File > Save As…** to save the XML file under the name _f84example.xml_ in the _MyPackage/examples_ folder


### Run the _f84example.xml_ file

The goal of this section is to create a debug configuration that is essentially a copy of the first one we created (in the section entitled “Create a debug configuration in Eclipse”) with the exception that the file that is run is _f84example.xml_ instead of _testF84.xml_. The easiest way to create the new configuration is to simply duplicate the existing one. Here are instructions for accomplishing that:

- Use the menu command **Run > Debug Configurations…** to open the **Debug Configurations** dialog box

- Under **Java Application**, select the “BeastMCMC” configuration

- Right-click (Control-click on a Mac) and choose **Duplicate** from the popup menu

- Select the new configuration, which for us was automatically named “BeastMCMC (1)”

- Click the **Arguments** tab and change the **Program arguments** field to _examples/f84example.xml_

- If necessary, move the _f84example.xml_ file inside the _MyPackage/examples_ directory so that its location matches the specification you just provided in the Program arguments field

You should now be able to click the **Debug** button to start an analysis of the _f84example.xml_ file.

The maximum likelihood estimates on a neighbor-joining tree evaluated using PAUP* were:

```xml
Tree                    1
-------------------------
-ln L         22466.61861
Base frequencies:
  A              0.277138
  C              0.273218
  G              0.107937
  T              0.341707
Ti/tv:
  k              0.970507
```

The corresponding posterior means we obtained from running the F84 package in BEAST 2 were as follows:

```xml
Base frequencies:
  A              0.276
  C              0.274
  G              0.108
  T              0.343
Ti/tv:
  k              0.928
```

The Bayesian estimate is lower than the maximum likelihood estimate due to the fact that we used the default prior on _k_, which is LogNormal with mean and standard deviation (on the log scale) of 1 and 1.25, respectively. Half the mass of this prior lies below 0.46, which pulls the posterior mean down slightly.

## Preparing your package for release

In order to publish your package, you will need to compile it outside of Eclipse and build a jar file that you can distribute. Here is an overview of the steps involved:

1. Compile your package using ant to create a zip file

2. Get your package published on the BEAST 2 web site so that others can install it over the internet

### Compile beast2 using ant

Navigate (in a Terminal window) to the _beast2_ project directory. If you have been following this tutorial, the folder should be _$HOME/Documents/workspace/beast2_. Type _ant_ at the command line. (If _ant_ cannot be found, you may need to install it from the Apache Ant website.) This will build BEAST 2 using the _build.xml_ file supplied with BEAST 2. It is necessary to build BEAST 2 again (even though we’ve already built it in Eclipse) because Eclipse places its object files and intermediate build products in a different directory than does ant.

### Compile MyPackage using ant

The next step is to create a _build.xml_ file within the _$HOME/Documents/workspace/MyPackage_ folder that tells ant how to build MyPackage. Create a new text file (using Eclipse or any way you like) just inside the _MyPackage_ directory. Name the new file _build.xml_ and enter the text below into the new file and save it.

```xml
 <!-- Build F84 model -->
 <project basedir="." default="build_jar_all_F84" name="BUILD_F84">
 	<description>
 	Build F84.
 	JUnit test is available for this build.
 	$Id: build_F84.xml $
 	</description>
 	<!-- set global properties for this build -->
 	<property name="srcF84" location="src" />
 	<property name="buildF84" location="build" />
 	<property name="libF84" location="lib" />
 	<property name="release_dir" value="release" />
 	<property name="distF84" location="${buildF84}/dist" />
 	<property name="beast2path" location="../beast2" />
 	<property name="libBeast2" location="${beast2path}/lib" />
 	<property name="srcBeast2" location="${beast2path}/src" />
 	<property name="beast2classpath" location="${beast2path}/build" />
 	<property name="Add_on_dir" value="${release_dir}/package" />
 <import file="${beast2path}/build.xml" />
 	<property name="main_class_BEAST" value="beast.app.BeastMCMC" />
 	<property name="report" value="${buildF84}/junitreport"/>
 	<path id="classpath">
 <pathelement path="${buildF84}"/>
 		<fileset dir="${libBeast2}" includes="junit-4.8.2.jar"/>
 <pathelement path="${beast2classpath}"/>
 	</path>
 	<!-- start -->
 	<target name="initF84">
 		<echo message="${ant.project.name}: ${ant.file}" />
 	</target>
 	<target name="cleanF84">
 	<delete dir="${buildF84}" />
 	</target>
 	
 	<!-- clean previous build, and then compile Java source code, and Juint test -->
 	<target name="build_all_F84" depends="cleanF84,compile-allF84,junitF84" description="Clean and Build all run-time stuff">
 	</target>
 	
 	<!-- clean previous build, compile Java source code, and Junit test, and make the beast.jar and beauti.jar -->
 	<target name="build_jar_all_F84" depends="cleanF84,compile-allF84,junitF84,dist_all_F84" description="Clean and Build all run-time stuff">
 	</target>
 	
 	<!-- No JUnit Test, clean previous build, compile Java source code, and make the F84.jar and beauti.jar -->
 	<target name="build_jar_all_F84_NoJUnitTest" depends="cleanF84,compile-allF84,dist_all_F84" description="Clean and Build all run-time stuff">
 	</target>
 
 	<!-- compile Java source code -->
 	<target name="compile-allF84" depends="initF84,compile-all">
 	<!-- Capture the path as a delimited property using the refid attribute -->
 	<property name="myclasspath" refid="classpath"/>
 	<!-- Emit the property to the ant console -->
 	<echo message="Classpath = ${myclasspath}"/>
 		<mkdir dir="${buildF84}" />
 		<!-- Compile the java code from ${srcF84} into ${buildF84} /bin -->
 		<javac srcdir="${srcF84}" destdir="${buildF84}" classpathref="classpath" fork="true" memoryinitialsize="256m" memorymaximumsize="256m">
 			<include name="beast/**/**" />
 			<!-- compile JUnit test classes -->
 			<include name="test/beast/**" />
 		</javac>
 		<echo message="Successfully compiled." />
 	</target>
 
 	<!-- make the beast.jar and beauti.jar -->
 	<target name="dist_all_F84" depends="compile-allF84" description="create F84 jar">
 		<!-- Create the distribution directory -->
 		<mkdir dir="${distF84}" />
 		<!-- Put everything in ${buildF84} into the beast.jar file -->
 		<jar jarfile="${distF84}/F84.jar">
 			<manifest>
 				<attribute name="Built-By" value="${user.name}" />
 				<attribute name="Main-Class" value="${main_class_BEAST}" />
 			</manifest>
 			<fileset dir="${buildF84}">
 				<include name="beast/**/*.class" />
 			</fileset>
 			<fileset dir="${beast2classpath}">
 				<include name="beast/**/*.class" />
 				<include name="beast/**/*.properties" />
 				<include name="beast/**/*.png" />
 				<include name="beagle/**/*.class" />
 				<include name="org/**/*.class" />
 			</fileset>
 		</jar>
 		<jar jarfile="${distF84}/F84.src.jar">
 			<fileset dir="${srcF84}">
 				<include name="beast/**/*.java" />
 				<include name="beast/**/*.png" />
 				<include name="beast/**/*.xsl" />
 			</fileset>
 </jar>
 		<jar jarfile="${dist}/F84.addon.jar">
 			<manifest>
 				<attribute name="Built-By" value="${user.name}" />
 			</manifest>
 			<fileset dir="${buildF84}">
 				<include name="beast/**/*.class" />
 				<include name="util/**/*.class" />
 				<include name="**/*.properties" />
 			</fileset>
 		</jar>
 	</target>
 
 	<!-- run beast.jar -->
 	<target name="run_F84">
 		<java jar="${distF84}/F84.jar" fork="true" />
 	</target>
 
 	<!-- JUnit test -->
 	<target name="junitF84">
 		<mkdir dir="${report}" />
 		<junit printsummary="yes"> <!--showoutput='yes'-->
 			<classpath>
 				<path refid="classpath" />
 				<path location="${buildF84}" />
 			</classpath>
 			<formatter type="xml" />
 			<batchtest fork="yes" todir="${report}">
 				<fileset dir="${srcF84}">
 <include name="test/**/*Test.java"/>
 				</fileset>
 				<fileset dir="${srcBeast2}">
 <include name="test/beast/integration/**/*Test.java"/>
 <exclude name="test/beast/integration/**/ResumeTest.java"/>
 				</fileset>
 			</batchtest>
 		</junit>
 		<echo message="JUnit test finished." />
 	</target>
 <target name="junitreport">
 		<junitreport todir="${report}">
 			<fileset dir="${report}" includes="*.xml"/>
 			<report format="frames" todir="${report}"/>
 		</junitreport>
 		<echo message="JUnit test report finished." />
 	</target>
 	<target name="addon"
 	depends="build_jar_all_F84_NoJUnitTest"
 	description="release BEAST 2 package version of F84">
 		<delete dir="${Add_on_dir}" />
 		<!-- Create the release directory -->
 		<mkdir dir="${Add_on_dir}" />
 		<mkdir dir="${Add_on_dir}/lib" />
 		<mkdir dir="${Add_on_dir}/templates" />
 		<copy todir="${Add_on_dir}/lib">
 			<fileset dir="${dist}" includes="F84.addon.jar" />
 		</copy>
 		<copy todir="${Add_on_dir}">
 			<fileset dir="${dist}" includes="F84.src.jar" />
 		</copy>
 		<copy todir="${Add_on_dir}/templates">
 			<fileset file="templates/F84.xml" />
 		</copy>
 		<jar jarfile="${dist}/F84.addon.zip">
 			<fileset dir="${Add_on_dir}">
 				<include name="**/*" />
 			</fileset>
 </jar>
 		<echo message="Package version release is finished." />
 	</target>
 </project>
```

You can now compile your package by changing directory to the _MyPackage_ folder and typing

```xml
ant addon
```

### Move the package to a place where BEAUti can find it

Once ant has finished compiling the package, you should have a _beast2/build/dist/F84.addon.zip_ file inside your Eclipse workspace directory. For testing purposes, this file should be copied to _$HOME/.beast2/F84_ and unzipped. If you have not yet run BEAUti outside of Eclipse, you may not yet have a _$HOME/.beast2_ directory. Also, you will need to create the _F84_ subdirectory inside the _$HOME/.beast2/_ directory.

```xml
mkdir -p ~/.beast2/2.6/F84
cp ~/workspace/beast2/build/dist/F84.addon.zip ~/.beast2/2.3/F84
cd ~/.beast2/2.6/F84
unzip F84.addon.zip
rm F84.addon.zip
```

This should create the following directory structure:

```xml
$HOME
    .beast2
        2.6
            F84
                META-INF
                    MANIFEST.MF
                lib
                    F84.addon.jar
                templates
```

