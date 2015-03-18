---
layout: default
title: 使用PHPExcel生产下拉菜单的cell
tags: php,excel
---


```java
class Pexcel{
 
 public function __construct() {
  $this->objPHPExcel = null;
  $this->excel_cell_num = 1;
 }

 public function download($category_id) {
  #$propertyList
          $this->_set_execl($propertyList);
 }
 
  private function _set_execl($propertyList){
     require_once APPPATH."libraries/Classes/PHPExcel.php"; 
     require_once APPPATH."libraries/Classes/PHPExcel/Writer/Excel5.php"; 
     if(is_null($this->objPHPExcel)){
         $this->objPHPExcel = new PHPExcel();
         $this->objWriter = new PHPExcel_Writer_Excel5($this->objPHPExcel);
            $objProps = $this->objPHPExcel->getProperties();   
            $objProps->setCreator("OKOKOK");       
            $objProps->setLastModifiedBy(" OKOKOK ");       
            $objProps->setTitle(" OKOKOK ");       
            $objProps->setSubject(" OKOKOK ");       
            $objProps->setDescription(" OKOKOK ");       
            $objProps->setKeywords(" OKOKOK ");       
            $objProps->setCategory(" OKOKOK "); 
            $this->objPHPExcel->setActiveSheetIndex(0);  
            $objActSheet = $this->objPHPExcel->getActiveSheet();
            $objActSheet->setTitle('Property');
     }
     
     foreach($propertyList as $key => $val){
         $chr = $this->_get_next_cell_chr($chr);
         $objActSheet->setCellValue($chr.'1', $key);
         if(is_array($val) && !empty($val)){
            $objValidation = $this->objPHPExcel->getActiveSheet()->getCell($chr."2")->getDataValidation();
            $formula1 = $this->_set_execl_cell_dd($objActSheet,$objValidation,$val);
            for($i=3;$i<100;$i++){
                $objValidation = $this->objPHPExcel->getActiveSheet()->getCell($chr.$i)->getDataValidation();
                $this->_set_execl_cell_dd($objActSheet,$objValidation,$val,$formula1);
            }
         }else{
            $objActSheet->setCellValue($chr."2", $val);
            for($i=3;$i<100;$i++){
                $objActSheet->setCellValue($chr.$i, $val);
            }
         }
     }
     
     $outputFileName = 'output'.date('Ymd-Hi').'.xls';
        header("Content-Type: application/force-download"); 
        header("Content-Type: application/octet-stream"); 
        header("Content-Type: application/download"); 
        header('Content-Disposition:inline;filename="'.$outputFileName.'"'); 
        header("Content-Transfer-Encoding: binary"); 
        header("Expires: Mon, 26 Jul 1997 05:00:00 GMT"); 
        header("Last-Modified: " . gmdate("D, d M Y H:i:s") . " GMT"); 
        header("Cache-Control: must-revalidate, post-check=0, pre-check=0"); 
        header("Pragma: no-cache"); 
        $this->objWriter->save('php://output'); 
        return;
 }

 private function _set_execl_cell_dd($objActSheet,$objValidation,$val_arr,$formula1 = ''){
        $objValidation->setType( PHPExcel_Cell_DataValidation::TYPE_LIST );
        $objValidation->setErrorStyle( PHPExcel_Cell_DataValidation::STYLE_INFORMATION );
        $objValidation->setAllowBlank(true);
        $objValidation->setShowInputMessage(false);
        $objValidation->setShowErrorMessage(false);
        $objValidation->setShowDropDown(true);
        if($formula1 !== ''){
            $objValidation->setFormula1($formula1);
        }else{
            $val = '"'.implode(',', $val_arr).'"';
            if(mb_strlen($val,'UTF-8') > 255){
                $char0 = $char = 'D';
                foreach($val_arr as $v){
                    $char = chr(ord($char)+1);
                    if($char == '['){
                        $char = 'A';
                        $char0 = chr(ord($char0)+1);
                    }
                    $objActSheet->setCellValue($char0.$char.$this->excel_cell_num, $v);
                }
                $formula1 = 'Property!$DE$'.$this->excel_cell_num.':$'.$char0.$char.'$'.$this->excel_cell_num;
                $this->excel_cell_num++;
            }else{
                $formula1 = $val;
            }
            $objValidation->setFormula1($formula1);
        }
        return $formula1;
 }
 
 private function _get_next_cell_chr($chr){
     if(empty($chr)){
         return 'A';
     }
     $chr1 = '';
     $chr2 = $chr;
     if(strlen($chr) > 1){
         $chr1 = substr($chr, 0, 1);
         $chr2 = substr($chr, 1, 1);
     }
     $chr2 = chr(ord($chr2)+1);
     if($chr2 == '['){
         if($chr1 == ''){
             $chr1 = 'A';
         }else{
             $chr1 = chr(ord($chr1)+1);
         }
         $chr2 = 'A';
     }
     return $chr1.$chr2;
 }
}
```
