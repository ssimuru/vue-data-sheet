<script>
// import jquery - using jquery for dom manipulation
import $ from 'jquery';

// define variable
// grid class name variable
var className = '';
// 기본적으로 input element를 edit 불가 상태로 놓고 double click이나 enter 이벤트 시에 edit 가능하도록 할 때 사용
var readOnly = false;
// ctrl 키가 눌렸는지 안 눌렸는지 구분할 때 사용
var ctrlKey = false;
// 최대 컬럼 수
var maxColNum = 0;
// 최대 행 수
var maxRowNum = 0;
// 마우스 drag 시 시작 포인트
var dragSrc = null;
// 마우스 drag 시 끝 포인트
var dragDest = null;

// 키보드 방향키를 눌렀을 때 scroll도 함께 event가 적용되는 것을 방지하기 위해 
// window의 keydown 이벤트에 preventDefault()를 사용해서 이벤트가 적용안되도록 함.
// 이 처리를 안하면 grid 안에서 방향키 이동 시 window의 스크롤도 함께 움직이게 된다. 
window.addEventListener("keydown", function(e) {
    // space and arrow keys
    if([37, 38, 39, 40].indexOf(e.keyCode) > -1) {
        e.preventDefault();
    }
}, false);

export default {
    data() {
        return {
            // grid 의 id 지정 시 각 grid를 구분할 수 있는 unique 한 key
            uniqueKey: Math.floor((Math.random() * 100000) + 1) // hash code를 생성하도록 수정 필요
        }
    },
    // grid를 사용하는 부모 컴포넌트로 부터 settings 객체를 가지고 옴
    props: ['settings'],
    // grid를 만듬
    render: function(createElement) {
        // class name을 vds__로 시작함
        className = 'vds__';
        var rtDivAttrs = { style: '' };
        // default는 편집이 가능한 mode 이고 lock은 편집이 불가능한 모드로 읽기만 가능함.
        // lock 일 경우 다른 이벤트 시 input의 readOnly를 변경할 수 없도록 처리해야 함.
        if(this.settings.type === 'default') {
            readOnly = true;
        } else if(this.settings.type === 'lock') {
            readOnly = false;
        }
        // width 속성을 지정한 경우 table의 width를 지정한 값으로 설정함
        if(this.settings.width != '') {
            rtDivAttrs.style = rtDivAttrs.style + 'width:' + this.settings.width + ';';
        }
        // height 속성을 지정한 경우 table의 height를 지정한 값으로 설정함
        if(this.settings.height != '') {
            rtDivAttrs.style = rtDivAttrs.style + 'height:' + this.settings.height + ';';
        }
        // width나 height 둘 중 하나라도 지정이 된 경우 데이터가 영영을 벗어났을 때 scroll이 생기도록 설정
        if(this.settings.width != '' | this.settings.height != '') {
            rtDivAttrs.style = rtDivAttrs.style + 'overflow: auto;';
        }

        return createElement(
            'div', {
                attrs: rtDivAttrs
            }, [
                // 최상위 element인 table을 만들고 하위에 header와 body를 생성함
                // table을 구분하기 위해서 id에 data-sheet-[unique key]를 설정함
                createElement('table', {
                    class: className + 'table',
                    attrs: {
                        id: 'data-sheet-' + this.uniqueKey
                    }
                }, [
                        this.genTHeader(),
                        this.genTBody()
                ])
            ]
        )
    },
    methods: {
        // w3school에 있는 table 정렬 참고
        sortTable: function(n) {
            var table, rows, switching, i, x, y, shouldSwitch, dir, switchcount = 0;
            table = $('#data-sheet-' + this.uniqueKey);
            switching = true;
            //Set the sorting direction to ascending:
            dir = "asc"; 
            /*Make a loop that will continue until
            no switching has been done:*/
            while (switching) {
                //start by saying: no switching is done:
                switching = false;
                rows = table.find('tr[class*=body]');
                /*Loop through all table rows (except the
                first, which contains table headers):*/
                for (i = 0; i < (rows.length - 1); i++) {
                    //start by saying there should be no switching:
                    shouldSwitch = false;
                    /*Get the two elements you want to compare,
                    one from current row and one from the next:*/
                    //x = rows[i].getElementsByTagName("TD")[n];
                    x = rows.eq(i).children().eq(n).find('input').val();
                    //y = rows[i + 1].getElementsByTagName("TD")[n];
                    y = rows.eq(i+1).children().eq(n).find('input').val();
                    /*check if the two rows should switch place,
                    based on the direction, asc or desc:*/
                    if (dir == "asc") {
                            if (x.toLowerCase() > y.toLowerCase()) {
                            //if so, mark as a switch and break the loop:
                            shouldSwitch= true;
                            break;
                        }
                    } else if (dir == "desc") {
                        if (x.toLowerCase() < y.toLowerCase()) {
                            //if so, mark as a switch and break the loop:
                            shouldSwitch = true;
                            break;
                        }
                    }
                }
                if (shouldSwitch) {
                    /*If a switch has been marked, make the switch
                    and mark that a switch has been done:*/
                    var colCnt = rows.eq(0).children().length;
                    for(var inx = 0; inx < colCnt; inx++) {
                        var elmt = rows.eq(i).find('label').eq(inx);
                        var prevClass = elmt.attr('class').match(/n-[0-9]*-[0-9]*/)[0];
                        elmt.removeClass(prevClass);
                        var clazz = prevClass.split('-');
                        elmt.addClass(clazz[0] + '-' + (i + 1) + '-' + clazz[2]);
                    }

                    for(var inx2 = 0; inx2 < colCnt; inx2++) {
                        var elmt2 = rows.eq(i + 1).find('label').eq(inx2);
                        var prevClass2 = elmt2.attr('class').match(/n-[0-9]*-[0-9]*/)[0];
                        elmt2.removeClass(prevClass2);
                        var clazz2 = prevClass2.split('-');
                        elmt2.addClass(clazz2[0] + '-' + (i) + '-' + clazz2[2]);
                    }

                    rows[i].parentNode.insertBefore(rows[i + 1], rows[i]);
                    //var pasteSrc = $(event.target.parentElement).attr('class').match(/n-[0-9]*-[0-9]*/)[0];
                    //var pasteSrc = rows[i].parentElement.attr('class').match(/n-[0-9]*-[0-9]*/)[0];
                    
                    
                    switching = true;
                    //Each time a switch is done, increase this count by 1:
                    switchcount ++;      
                } else {
                    /*If no switching has been done AND the direction is "asc",
                    set the direction to "desc" and run the while loop again.*/
                    if (switchcount == 0 && dir == "asc") {
                        dir = "desc";
                        switching = true;
                    }
                }
            }
            var colHeader = table.find('tr[class*=header]').children().eq(n);
            $('.order-dir').remove();
            if(dir == "asc") {
                colHeader.append('<span class="order-dir">&nbsp;&uarr;</span>');
            } else {
                colHeader.append('<span class="order-dir">&nbsp;&darr;</span>');
            }
            $(".clicked > input").select();
        },
        // table의 header를 생성함
        genTHeader: function() {
            var childrenHeader = [];
            var headers = this.settings.header;

            if(headers == null) {
                var cols = this.settings.cols;

                if(cols === 0 | cols == null) {
                    alert('config \'cols\' or \'header\' property')
                }

                for(var i = 0; i < cols; i++ ) {
                    childrenHeader[i] = this.$createElement('th', {class: className + 'table--header-col'}, ('COL' + i));
                }
            } else {
                var vm = this;
                headers.forEach((header, index) => {
                    childrenHeader[index] = this.$createElement('th', {
                        class: className + 'table--header-col',
                        on: {
                            click: function() {
                                vm.sortTable(index);
                            }
                        }
                        }, header.name);
                })
                
            }
            var children = [this.$createElement('tr', {class: className + 'table--header-row'}, childrenHeader)];
        
            return this.$createElement('thead', {class: className + 'table--header'}, children);
        },
        genTBody: function() {
            var children = [this.genBody()];

            return this.$createElement('tbody', {class: className + 'table--body'}, children);
        },
        genBody: function() {
            var childrenHeader = [];
            var body = this.settings.gridData;
            //var vm = this;
            maxRowNum = body.length;
            if(body == '' | body == null) {
                alert('config \'gridData\' property')
            } else {
                body.forEach((content, index) => {
                    var children = [];
                    maxColNum = content.length;

                    content.forEach((data, num) => {
                        var inputAttrs = {};
                        inputAttrs.value = data;
                        inputAttrs.name = 'vds-name' + num;
                        var type = this.settings.header[num].type;
                        if(readOnly == true) {
                            inputAttrs.readOnly = 'readOnly';
                        }

                        var elementForType = [];
                        if(type == 'input') {
                            elementForType[num] = this.$createElement('label', { 
                                class: className + 'table--body-col-label' + ' n-' + index + '-' + num,
                                attrs: { for: 'vds-name' + num }, 
                                on: {
                                    click: function(event) {
                                        var checkClicked = event.target.parentElement.className;
                                        
                                        if(checkClicked.indexOf('clicked') == -1) {
                                            //console.log(this);
                                            //clicked라는 class를 가지고 있는 DOM element를 clicked를 해제시키는 로직 추가
                                            $('.clicked > input').attr('readOnly', 'readOnly');
                                            $('.clicked').removeClass('clicked');
                                            //------------------------------------------------------------------------
                                            // 기존 선택 영역 해제
                                            $('.range_lt').removeClass('range_lt');
                                            $('.range_lm').removeClass('range_lm');
                                            $('.range_lb').removeClass('range_lb');
                                            $('.range_ct').removeClass('range_ct');
                                            $('.range_cm').removeClass('range_cm');
                                            $('.range_cb').removeClass('range_cb');
                                            $('.range_rt').removeClass('range_rt');
                                            $('.range_rm').removeClass('range_rm');
                                            $('.range_rb').removeClass('range_rb');
                                            $('.range_l').removeClass('range_l');
                                            $('.range_c').removeClass('range_c');
                                            $('.range_r').removeClass('range_r');
                                            $('.range_t').removeClass('range_t');
                                            $('.range_m').removeClass('range_m');
                                            $('.range_b').removeClass('range_b');
                                            //---------------------------------------------------------------------

                                            event.target.parentElement.className = event.target.parentElement.className + ' clicked'
                                        } else {
                                            var pClassName = event.target.parentElement.className;
                                            event.target.parentElement.className = pClassName.replace(' clicked', '');
                                        }
                                    },
                                    keydown: function(event) {
                                        if(event.keyCode == 17) ctrlKey = true;

                                        if(event.keyCode == 67 | 
                                            event.keyCode == 37 | 
                                            event.keyCode == 38 |
                                            event.keyCode == 39 |
                                            event.keyCode == 40 ) {
                                           //console.log(dragSrc.className);  
                                           //방향키가 입력이 되었을 경우, scroll에 element가 scroll 영역안에 가려져 있으면 scroll이 보이도록 조정함.
                                           // dom element의 상대적 위치를 구하는 부분이 어려움. 나중에 처리
                                        //    var position = event.target.offsetParent.offsetTop;
                                        //    var viewPosition = vm.settings.height.replace('px', '');
                                        //    if( parseInt(position) >= parseInt(viewPosition) ) {
                                        //        event.target.scrollIntoView()
                                        //    }
                                           
                                        } else {
                                            if($(".clicked > input").attr('readOnly')) {
                                                $(".clicked > input").removeAttr('readOnly').select();
                                            }
                                        }                                      
                                    },
                                    paste: function(event) {
                                        //.clicked 를 가지고 있는 element를 시작으로 
                                        //데이터를 체워나가면 됨
                                        var clipData = event.clipboardData.getData('text');
                                        //var clipData = event;
                                        var pasteSrc = $(event.target.parentElement).attr('class').match(/n-[0-9]*-[0-9]*/)[0];
                                        var startRow = parseInt(pasteSrc.split('-')[1]);
                                        var startCol = parseInt(pasteSrc.split('-')[2]);

                                        var clipRow = clipData.split('\n');
                                        //console.log(clipRow)
                                        for(var rowinx = 0; rowinx < clipRow.length; rowinx++) {
                                            var rowNum = startRow + rowinx;
                                            var clipCol = clipRow[rowinx].split('	');
                                            
                                            for(var colinx = 0; colinx < clipCol.length; colinx++) {
                                                var colNum = startCol + colinx;
                                                var className = 'n-' + rowNum + '-' + colNum;
                                                $('.' + className + ' > input').val(clipCol[colinx]);
                                                body[rowNum][colNum] =  clipCol[colinx];
                                            }
                                        }
                                        

                                        event.stopPropagation();
                                        event.preventDefault();
                                    },
                                    keyup: function(event) {
                                        //console.log($(".clicked").length);
                                        var rowCol = null;
                                        if($(".clicked").length > 0) {
                                            rowCol = $(".clicked").attr('class').match(/n-[0-9]*-[0-9]*/)[0];
                                        } else {
                                            return
                                        }
                                        var temp = rowCol.split("-");
                                        

                                        if(ctrlKey == true && event.keyCode == 67) {
                                            $(".clicked > input").select();
                                            document.execCommand('copy');
                                            ctrlKey = false;
                                        } else if(ctrlKey == true && event.keyCode == 86) {
                                            //console.log(dragSrc.className); 
                                        } else if(event.keyCode == 37) {
                                           
                                            
                                            if(temp[2] > 0) {
                                                var leftRowCol = temp[0] + "-" + temp[1] + "-" + (parseInt(temp[2]) - 1);
                                                
                                                $('.clicked > input').attr('readOnly', 'readOnly');
                                                $(".clicked").removeClass("clicked");
                                                
                                                $("." + leftRowCol).addClass("clicked");
                                            } else {
                                                return
                                            }
                                        } else if(event.keyCode == 38) {
                                            
                                            if(temp[1] > 0) {
                                                var upRowCol = temp[0] + "-" + (parseInt(temp[1]) - 1) + "-" + temp[2];
                                                
                                                $('.clicked > input').attr('readOnly', 'readOnly');
                                                $(".clicked").removeClass("clicked");
                                                
                                                $("." + upRowCol).addClass("clicked");
                                            } else {
                                                return
                                            }
                                        } else if(event.keyCode == 39 | event.keyCode == 9) {
                                            
                                            if(temp[2] < (maxColNum - 1)) {
                                                var rightRowCol = temp[0] + "-" + temp[1] + "-" + (parseInt(temp[2]) + 1);

                                                $('.clicked > input').attr('readOnly', 'readOnly');
                                                $(".clicked").removeClass("clicked");
                                                
                                                $("." + rightRowCol).addClass("clicked");
                                            } else {
                                                return
                                            }
                                        } else if(event.keyCode == 40 | event.keyCode == 13) {
                                           
                                            if(temp[1] < (maxRowNum - 1)) {
                                                var downRowCol = temp[0] + "-" + (parseInt(temp[1]) + 1) + "-" + temp[2];
                                                
                                                $('.clicked > input').attr('readOnly', 'readOnly');
                                                $(".clicked").removeClass("clicked");

                                                $("." + downRowCol).addClass("clicked");
                                            } else {
                                                return
                                            }
                                        } else {
                                            // dragDest = event.relatedTarget;
                                            // // eslint-disable-next-line
                                            // console.log(dragDest.tagName); 
                                        }
                                        event.stopPropagation();
                                        event.preventDefault();
                                    },
                                    mousedown: function(event) {
                                        dragSrc = $(event.target.parentElement).attr('class').match(/n-[0-9]*-[0-9]*/)[0];
                                        // eslint-disable-next-line
                                    },
                                    mouseup: function(event) {
                                        dragDest = $(event.target.parentElement).attr('class').match(/n-[0-9]*-[0-9]*/)[0];

                                        if(dragSrc != dragDest) {
                                            // 기존 선택 영역 해제
                                            $('.range_lt').removeClass('range_lt');
                                            $('.range_lm').removeClass('range_lm');
                                            $('.range_lb').removeClass('range_lb');
                                            $('.range_ct').removeClass('range_ct');
                                            $('.range_cm').removeClass('range_cm');
                                            $('.range_cb').removeClass('range_cb');
                                            $('.range_rt').removeClass('range_rt');
                                            $('.range_rm').removeClass('range_rm');
                                            $('.range_rb').removeClass('range_rb');
                                            $('.range_l').removeClass('range_l');
                                            $('.range_c').removeClass('range_c');
                                            $('.range_r').removeClass('range_r');
                                            $('.range_t').removeClass('range_t');
                                            $('.range_m').removeClass('range_m');
                                            $('.range_b').removeClass('range_b');
                                            //---------------------------------------------------------------------

                                            var tempSrc = dragSrc.split("-");
                                            var tempDest = dragDest.split("-");

                                            var rowNum = (parseInt(tempDest[1]) - parseInt(tempSrc[1])) + 1;
                                            var colNum = (parseInt(tempDest[2]) - parseInt(tempSrc[2])) + 1;

                                            var copyValue = '';

                                            for(var row = 0; row < rowNum; row++) {
                                                var suffix = '';
                                                if(row == 0) {
                                                    suffix = 't'
                                                } else if(row == (rowNum - 1)) {
                                                    suffix = 'b'
                                                } else {
                                                    suffix = 'm'
                                                }
                                                if(rowNum == 1) suffix = '';

                                                for(var col = 0; col < colNum; col++) {
                                                    var prefix = '';
                                                    if(col == 0) {
                                                        prefix = 'l'
                                                    } else if(col == (colNum - 1)) {
                                                        prefix = 'r'
                                                    } else {
                                                        prefix = 'c'
                                                    }
                                                    if(colNum == 1) prefix = '';
                                                    var className = 'range_' + prefix + suffix;
                                                    // eslint-disable-next-line
                                                    var classColNum = (parseInt(col) + parseInt(tempSrc[2]));
                                                    var classRowNum = (parseInt(row) + parseInt(tempSrc[1]));

                                                    //console.log('n-' + classRowNum + '-' + classColNum + ' :' + className);
                                                    $('.n-' + classRowNum + '-' + classColNum).addClass(className);
                                                    //$('.n-' + classRowNum + '-' + classColNum + '>input').select();
                                                    if(col != 0) {
                                                        copyValue = copyValue + '	' + $('.n-' + classRowNum + '-' + classColNum + '>input').val();
                                                    } else {
                                                        copyValue = copyValue + $('.n-' + classRowNum + '-' + classColNum + '>input').val();
                                                    }
                                                }
                                                if(row != (rowNum - 1)) {
                                                    copyValue = copyValue + '\n';
                                                }
                                                
                                            }

                                            //기존 선택되어 있던 cell 선택 해제하고 cell 안에 input은 readOnly로 변경
                                            $('.clicked > input').attr('readOnly', 'readOnly');
                                            $('.clicked').removeClass('clicked');   
                                            //display: none;
                                            $('body').append("<textarea class='temp_copy_value' style='position: \"absolute\"; left: \"-9999px\";'></textarea>");
                                            $('.temp_copy_value').val(copyValue).select();
                                            document.execCommand('copy');
                                            $('.temp_copy_value').remove();
                                        }
                                    }
                                }}, [
                                    this.$createElement('input', {
                                        class: className + 'table--body-col-input',
                                        attrs: inputAttrs,
                                        on: {
                                            input: function (event) {
                                                
                                                body[index][num] =  event.target.value;
                                                
                                            }
                                        }
                                    }, data)
                            ]);
                        } else {
                            elementForType[num] = this.$createElement('label', {}, [
                                this.$createElement('input', {
                                    class: className + 'table--body-col-input',
                                    attrs: inputAttrs,
                                    on: {
                                        input: function (event) {
                                            
                                            body[index][num] =  event.target.value;
                                            // $(".clicked").style.width = ((el.value.length+1) * 7) + 'px';
                                            // $(".clicked > input").style.width = ((el.value.length+1) * 7) + 'px'
                                            //function resize() {el.style.width = ((el.value.length+1) * 7) + 'px'}
                                        }
                                    }
                                }, data)
                            ]);
                        }
                        children[num] = this.$createElement('td', {class: className + 'table--body-col'}, elementForType);
                    })

                    childrenHeader[index] = this.$createElement('tr', {class: className + 'table--body-row'}, children);
                })
            }

            return childrenHeader;
        }
    }    
}
</script>


<style src="../css/vds-style.css" scoped>
</style>
