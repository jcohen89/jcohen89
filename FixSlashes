<!DOCTYPE html>
<html>
  <head>
    <link type='text/css' rel='stylesheet' href='style.css'/>
    <title>PHP!</title>
  </head>
  <body>

<?php
// define initial variables
ini_set("auto_detect_line_endings", true);
$fieldseparator="\t";
$linesseparator="\n";
$addauto=0;
$linesduped=0;
$linesparsed=0;
$NumGPLsProcessed=0;
$NumContainedEntrez=0;
$NumContainedAccession=0;
$NumContainedGeneSym=0;
//Create file to dump GPLs without Gene Identifiers
$GPLsWithoutEntrez="GPLsWithoutEntrez.txt";
$OpenGPLwoEntrez=fopen($GPLsWithoutEntrez, 'w');
fclose($OpenGPLwoEntrez);

// Initialize variables that will be used to produce the URLs called
$URLtop="http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=";
$URLbot="&targ=self&view=full&form=text";
$URL=$URLtop . $GPLID['GPL'] . $URLbot;


//open connection to SQL
require_once 'login.php';
$con = mysqli_connect($db_hostname, $db_username, $db_password, $db_database) or die(mysqli_error($con));
if (mysqli_connect_errno()) {
        die('Could not connect: ' . mysqli_connect_error());
        }

// generate list of Tables to create and populate based on GPL column of gpl table
$samplelist = mysqli_query($con, "SELECT GPL FROM `gpl` WHERE ID =  72");
while($GPLID = mysqli_fetch_array($samplelist)) {
	$FoundEntrez=0;
	$FoundAccession=0;
	$filepath = $URLtop . $GPLID['GPL'] . $URLbot;
//        $filepath = 'http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=gpl2112&targ=self&view=quick&form=text';
        $size = filesize($filepath);
        $searchfor = "^PLATFORM";
        // get the file contents, assuming the file to be readable (and exist)
        $contents = file_get_contents($filepath);
        // escape special characters in the query
        $pattern = preg_quote($searchfor, '/');
        // finalise the regular expression, matching the whole line
        $pattern = "/^.*$pattern.*\$/m";
        // search, and store all matching occurences in $matches
        if(preg_match_all($pattern, $contents, $matches)) {
        }
        else{
                echo "No matches found";
        }
//	$SearchforSlashes=' /// ';
//	echo $SearchforSlashes."<br />";
//	$contents=str_replace($SearchforSlashes, "\t", $contents);
	//echo $contents;
        $mkstr = (string)implode($matches[0]);
        $GPL = substr($mkstr, 12);
        $GPL = str_replace(' ', '', $GPL);
        $GPL = preg_replace('/\s+/', '', $GPL);
	$NumGPLsProcessed++;
	echo $GPL;

//Search for gene identifiers Entrez, Accession, or Gene Symbol
	$SearchForEntrez="Entrez";
        $PatternTwo = preg_quote($SearchForEntrez, '/');
        $PatternTwo = "/^.*$PatternTwo.*\$/mi";
	$SearchForAccession = "Accession";
	$PatternThree = preg_quote($SearchForAccession, '/');
        $PatternThree = "/^.*$PatternThree.*\$/mi";
	$SearchForGeneSym = "Gene sym";
        $PatternFour = preg_quote($SearchForGeneSym, '/');
        $PatternFour = "/^.*$PatternFour.*\$/mi";



	$result = mysqli_query($con, $CreateTable);
        $handle = @fopen($filepath, "r");
        if ($handle) {
        	while (($buffer = fgets($handle, 4096)) !== false) {
                	$filecontent = $buffer . "<br />";
                        $linearray=explode($fieldseparator,$filecontent);
                        // Insert data
                         switch($linearray[0]) {
                         	case strpos($linearray[0], "#"):
					if(preg_match_all($PatternTwo, $linearray[0], $EntrezMatches)) {
						 foreach ($EntrezMatches as $EntrezMatch) {
							$EntrezLine = (string)implode($EntrezMatch);
							//echo $EntrezLine;
							$NumContainedEntrez++;
							$AutoInc=0;
							$GeneIDEntrez = "UPDATE gpl SET Gene_Identifier='Entrez' WHERE GPL = '".$GPL."'";
							$RunGeneIDEntrez = mysqli_query($con, $GeneIDEntrez);
							$SearchForEntrezCol= strpos($EntrezLine, "#");
							$EntrezEqualsPosition = strrpos($EntrezLine, "=");
							$EntrezColumnName = substr($EntrezLine, $SearchForEntrezCol+1, $EntrezEqualsPosition - 2);
							echo "<br />".$EntrezColumnName;
							$CreateTable = "CREATE TABLE " . $GPL . " (ID BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY, Ref_ID VARCHAR(255), Gene_Identifer VARCHAR(255));";
                                			// echo $CreateTable;
                                			$result = mysqli_query($con, $CreateTable);
							$FoundEntrez=1;
						}
					}
					else{
					//	echo "NOT ENTREZ";
					}
			 	break;
				case strpos($linearray[0], "ID"):
                                	if($EntrezArrayPosition = array_search($EntrezColumnName, $linearray)) {
						$GeneIDColumn=$EntrezArrayPosition;
					}
					elseif($AccessionArrayPosition = array_search($AccessionColumnName, $linearray)){
						$GeneIDColumn=$AccessionArrayPosition;
					}
					else{
					//dump line into file
					}
				case strpos($linearray[0], "!"):
				case strpos($linearray[0], "^"):
				case strpos($linearray[0], "//"):
				break;
                                default:
					 $AutoInc++;
					 $query = "insert into `".$GPL."` values('".$AutoInc."','" . $linearray[0] . "','" . $linearray[$GeneIDColumn] . "');";
                                       	 //echo $query;
                                         $result = mysqli_query($con, $query);

	//if (!feof($handle)) {
        //	echo "Error: unexpected fgets() fail\n";
       // }
        // fclose($handle);


			}
		}
	}
	if($FoundEntrez!=1) {
		echo "NO ENTREZ";
		$handle = @fopen($filepath, "r");
		if ($handle) {
                	while (($buffer = fgets($handle, 4096)) !== false) {
                        $filecontent = $buffer . "<br />";
                        $linearray=explode($fieldseparator,$filecontent);
                        // Insert data
                         	switch($linearray[0]) {
                                	case strpos($linearray[0], "#"):
						if(preg_match_all($PatternThree, $linearray[0], $AccessionMatches)) {
                                                //echo print_r($AccessionMatches);
                                             		foreach ($AccessionMatches as $AccessionMatch) {
                                                        	$AccessionMatch=(string)implode($AccessionMatch);
                                                        	$SearchForAccessionCol= strpos($AccessionMatch, "#");
                                                        	echo $SearchForAccessionCol."<br />";
                                                        	$SearchForAccessionEqualPos=stripos($AccessionMatch,"=");
                                                        	echo $SearchForAccessionEqualPos;
                                                        	$AccessionColumnName=substr($AccessionMatch, 1, $SearchForAccessionEqualPos-2);
                                                        	$NumContainedAccession++;
								$FoundAccession=1;
                                                        	$GeneIDAccession = "UPDATE gpl SET Gene_Identifier='Accession' WHERE GPL = '".$GPL."'";
                                                        	$RunGeneIDAccession = mysqli_query($con, $GeneIDAccession);

                                                     		 //$AccessionColLine = (string)implode($AccessionMatches[0]);
                                                     		 echo "<br />".$AccessionColumnName."<br />";
                                                        	 $CreateTable = "CREATE TABLE " . $GPL . " (ID BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY Ref_ID VARCHAR(255), Gene_Identifer VARCHAR(255));";
                                                     		 $result = mysqli_query($con, $CreateTable);
							}
                                                }
					break;
	                                case strpos($linearray[0], "ID"):
        	                                if($EntrezArrayPosition = array_search($EntrezColumnName, $linearray)) {
                	                                $GeneIDColumn=$EntrezArrayPosition;
                        	                }
                                	        elseif($AccessionArrayPosition = array_search($AccessionColumnName, $linearray)){
                                        	        $GeneIDColumn=$AccessionArrayPosition;
                                       		}
    	                               case strpos($linearray[0], "!"):
            	                       case strpos($linearray[0], "^"):
                                       case strpos($linearray[0], "//"):
                   	               break;
                               	       default:
                                     	      $query = "insert into `".$GPL."` (Ref_ID, Gene_Identifier) VALUES ('" . $linearray[0] . "','" . $linearray[$GeneIDColumn] . "');";
                                              //echo $query;
                                              $result = mysqli_query($con, $query);


				}
			}
		}
		if($FoundAccession!=1) {
		echo "NO GENE ID";
		echo $GPL."<br />";
		$query = "insert into `NoGeneID` (GPL, Column_Names) values('" . $GPL . "','" . print_r($linearray). "');";
                //echo $query;
                $result = mysqli_query($con, $query);


		//$handle = @fopen($filepath, "r");
              //  if ($handle) {
                    //    while (($buffer = fgets($handle, 4096)) !== false) {
                      //  $filecontent = $buffer . "<br />";
                  //      $linearray=explode($fieldseparator,$filecontent);
                        // Insert data
                            //    switch($linearray[0]) {
                                  //      case strpos($linearray[0], "ID"):
				//		$linearray=(string)implode($linearray);
				//		echo $linearray;
				//		$query = "insert into `NoGeneID` (GPL, Column_Names) values('" . $GPL . "','" . print_r($linearray). "');";
                                                //echo $query;
                                  //              $result = mysqli_query($con, $query);
				//	break;
		//			default:
		//		}
		//	}
		//}
		}
	}
//	$DeleteMistakes="DELETE FROM `".$GPL."` WHERE REF_ID LIKE '%//%';";
//	$RunDeleteMistakes=mysqli_query($con, $DeleteMistakes);

	/* if(FoundAccession=) {
                echo "NO GENE ID";
                echo $GPL."<br />";
                $query = "insert into `NoGeneID` (GPL, Column_Names) values('" . $GPL . "','" . print_r($linearray). "');";
                //echo $query;
                $result = mysqli_query($con, $query);
	}*/
}
mysqli_close($con);
fclose($GPLsWithoutEntrez);

//echo $GPL ;
echo $NumContainedEntrez ."<br />";
echo $NumContainedAccession."<br />";
echo $NumGPLsProcessed ."<br />";

//$FractionWithEntrez=$NumContainedEntrez/$NumGPLsProcessed;
//echo $FractionWithEntrez;

 ?>
  </body>
</html>
